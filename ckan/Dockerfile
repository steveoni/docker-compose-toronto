FROM viderum/ckan:2.9.9-dev
## dummy
USER root

ENV CKAN_INI /srv/app/production.ini


# Set timezone
ENV TZ=UTC

# Install GDAL dependencies and build tools
RUN apk add --no-cache \
    build-base \
    g++ \
    gdal \
    gdal-dev \  
    geos \
    geos-dev \
    libffi-dev \
    openssl-dev \
    libxml2-dev \
    libxslt-dev \
    libjpeg-turbo-dev \
    zlib-dev \
    python3-dev

RUN pip install -e git+https://github.com/ckan/ckanext-pdfview.git#egg=ckanext-pdfview
    # pip install -e git+https://github.com/open-data-toronto/ckan-customization-open-data-toronto.git@v2.6.0#egg=ckanext-opendatatoronto


COPY setup/prerun.py ${APP_DIR}

COPY setup/supervisor.worker.conf /etc/supervisord.d/worker.conf
# COPY setup/supervisord.conf /etc/supervisord.conf
COPY setup/start_ckan.sh ${APP_DIR}
RUN chmod +x ${APP_DIR}/start_ckan.sh
COPY docker-entrypoint.d/* /docker-entrypoint.d/



RUN ckan config-tool /srv/app/production.ini "ckan.plugins = ${CKAN__PLUGINS}"
RUN ckan config-tool /srv/app/production.ini  "WTF_CSRF_SECRET_KEY=$(python3 -c 'import secrets; print(secrets.token_urlsafe())')" &&\
    ckan config-tool /srv/app/production.ini "WTF_CSRF_TIME_LIMIT= 86400" && \
    ckan config-tool /srv/app/production.ini  "beaker.session.secret=$(python3 -c 'import secrets; print(secrets.token_urlsafe())')" && \
    ckan config-tool /srv/app/production.ini "ckan.site_url=http://ckan:5000" && \
    ckan config-tool /srv/app/production.ini "ckan.datapusher.callback_url_base=http://ckan:5000" && \
    ckan config-tool /srv/app/production.ini "ckan.datapusher.url=http://datapusher:8800"

# fix ssl error with latest pyopenssl and cryptograpy version downgrade
RUN pip install --upgrade pyOpenSSL
RUN pip install cryptography==38.0.0

RUN ckan config-tool /srv/app/production.ini "ckan.max_resource_size = 3145728" 
USER root
RUN mkdir -p ${APP_DIR}/src_extensions
ENV SRC_EXTENSIONS_DIR=${APP_DIR}/src_extensions

RUN chown -R ckan:ckan ${APP_DIR}/src_extensions && \
    chmod -R 755 ${APP_DIR}/src_extensions

CMD ["sh", "-c", "${APP_DIR}/start_ckan.sh"]
