ARG NGINX_VERSION
FROM nginx:${NGINX_VERSION}

ARG SERVICE_DIR="./nginx"
COPY ./.shared/scripts/ /tmp/scripts/
RUN chmod -R 777 /tmp/scripts/

# set timezone
ARG TZ=UTC
RUN /tmp/scripts/set_timezone.sh ${TZ}

# add users
ARG APP_USER=www-data
ARG APP_GROUP=www-data
ARG APP_USER_ID=1000
ARG APP_GROUP_ID=1000

RUN /tmp/scripts/create_user.sh ${APP_USER} ${APP_GROUP} ${APP_USER_ID} ${APP_GROUP_ID}

RUN /tmp/scripts/install_software.sh

# forward request and error logs to docker log collector
RUN ln -sf /dev/stdout /var/log/nginx/access.log \
	&& ln -sf /dev/stderr /var/log/nginx/error.log

# nginx config
COPY ${SERVICE_DIR}/nginx.conf /etc/nginx/
ARG APP_USER
RUN /tmp/scripts/modify_config.sh /etc/nginx/nginx.conf \
    "__APP_USER" \
    "${APP_USER}" \
 && /tmp/scripts/modify_config.sh /etc/nginx/nginx.conf \
    "__APP_GROUP" \
    "${APP_GROUP}" \
;

# nginx app config
COPY ${SERVICE_DIR}/sites-available/* /etc/nginx/sites-available/
ARG APP_CODE_PATH
ARG APP_HOST
ARG APP_CERT_NAME
ARG APP_CERT_KEY_NAME
ARG APP_CERT_DHPARAM_NAME
ARG COMPOSE_PROJECT_NAME
RUN /tmp/scripts/modify_config.sh /etc/nginx/sites-available/default.conf \
    "__NGINX_ROOT" \
    "${APP_CODE_PATH}/public" \
;
RUN /tmp/scripts/modify_config.sh /etc/nginx/sites-available/default.conf \
    "__APP_HOST" \
    "${APP_HOST}" \
;
RUN /tmp/scripts/modify_config.sh /etc/nginx/sites-available/default.conf \
    "__APP_CERT_NAME" \
    "${APP_CERT_NAME}" \
;
RUN /tmp/scripts/modify_config.sh /etc/nginx/sites-available/default.conf \
    "__APP_CERT_KEY_NAME" \
    "${APP_CERT_KEY_NAME}" \
;
RUN /tmp/scripts/modify_config.sh /etc/nginx/sites-available/default.conf \
    "__APP_CERT_DHPARAM_NAME" \
    "${APP_CERT_DHPARAM_NAME}" \
;
RUN /tmp/scripts/modify_config.sh /etc/nginx/sites-available/default.conf \
    "__COMPOSE_PROJECT_NAME" \
    "${COMPOSE_PROJECT_NAME}" \
;

# workdir
WORKDIR /etc/nginx/

# cleanup
RUN /tmp/scripts/cleanup.sh

CMD ["nginx"]

EXPOSE 80 443
