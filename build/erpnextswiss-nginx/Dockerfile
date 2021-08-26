ARG NODE_IMAGE_TAG=14-buster-slim
ARG DOCKER_REGISTRY_PREFIX=frappe
ARG IMAGE_TAG=master

FROM node:${NODE_IMAGE_TAG} AS erpnext

ARG GIT_REPO=https://github.com/frappe/erpnext
ARG GIT_BRANCH=master
ARG FRAPPE_BRANCH=${GIT_BRANCH}

COPY install_app.sh /install_app
RUN chmod +x /install_app && \
  apt-get update -y && \
  apt-get install build-essential git python2 -y && \
  apt-get clean && \
  rm -rf /var/lib/apt/lists/*

RUN /install_app erpnext ${GIT_REPO} ${GIT_BRANCH} ${FRAPPE_BRANCH}

FROM node:${NODE_IMAGE_TAG} AS erpnextswiss

ARG GIT_REPO=https://github.com/frappe/erpnext
ARG ERPNEXTSWISS_GIT_BRANCH=master
ARG FRAPPE_BRANCH=${GIT_BRANCH}

COPY install_app.sh /install_app
RUN chmod +x /install_app && \
  apt-get update -y && \
  apt-get install build-essential git python2 -y && \
  apt-get clean && \
  rm -rf /var/lib/apt/lists/*

RUN /install_app erpnextswiss https://github.com/libracore/erpnextswiss ${ERPNEXTSWISS_GIT_BRANCH} ${FRAPPE_BRANCH}

FROM ${DOCKER_REGISTRY_PREFIX}/frappe-nginx:${IMAGE_TAG}

COPY --from=erpnext /home/frappe/frappe-bench/sites/ /var/www/html/
COPY --from=erpnext /rsync /rsync

COPY --from=erpnextswiss /home/frappe/frappe-bench/sites/ /var/www/html/
COPY --from=erpnextswiss /rsync /rsync

RUN echo "erpnext" >> /var/www/html/apps.txt
RUN echo "erpnextswiss" >> /var/www/html/apps.txt

VOLUME [ "/assets" ]

ENTRYPOINT ["/docker-entrypoint.sh"]
CMD ["nginx", "-g", "daemon off;"]
