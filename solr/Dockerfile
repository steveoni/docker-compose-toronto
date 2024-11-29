FROM --platform=linux/amd64 solr:6.3-slim as solr-6-amd64

ENV ARCH=amd64

#
# ARM64
#
FROM --platform=linux/arm64 arm64v8/solr:6.3-slim as solr-6-arm64

ENV ARCH=arm64

#
# Final
#
# Choose the base image based on the target architecture
FROM solr-6-arm64 as solr
MAINTAINER Open Knowledge

# Enviroment
ENV SOLR_CORE ckan
# Adjust to your needs
ENV CKAN_VERSION dev-v2.7


# User
USER root

ENV SOLR_HEAP="1024m" \
    SOLR_DEFAULT_CONFIG_SET="search_api_solr_4.2.1" \
    SOLR_CONFIG_DIR="/opt/solr/server/solr/configsets" \
    SOLR_SCHEMA_FILE="$SOLR_CONFIG_DIR/ckan/conf/managed-schema" \
    SOLR_OPTS="-XX:-UseLargePages"

# Create Directories
RUN mkdir -p /opt/solr/server/solr/$SOLR_CORE
RUN mkdir -p /opt/solr/server/solr/$SOLR_CORE/conf
RUN mkdir -p /opt/solr/server/solr/$SOLR_CORE/data
RUN mkdir -p /opt/solr/server/solr/configsets/$SOLR_CORE/

# Adding Files
ADD solrconfig.xml \
    https://raw.githubusercontent.com/ckan/ckan/$CKAN_VERSION/ckan/config/solr/schema.xml \
    https://raw.githubusercontent.com/apache/lucene-solr/releases/lucene-solr/6.6.6/solr/server/solr/configsets/basic_configs/conf/currency.xml \
    https://raw.githubusercontent.com/apache/lucene-solr/releases/lucene-solr/6.6.6/solr/server/solr/configsets/basic_configs/conf/synonyms.txt \
    https://raw.githubusercontent.com/apache/lucene-solr/releases/lucene-solr/6.6.6/solr/server/solr/configsets/basic_configs/conf/stopwords.txt \
    https://raw.githubusercontent.com/apache/lucene-solr/releases/lucene-solr/6.6.6/solr/server/solr/configsets/basic_configs/conf/protwords.txt \
    https://raw.githubusercontent.com/apache/lucene-solr/releases/lucene-solr/6.6.6/solr/server/solr/configsets/data_driven_schema_configs/conf/elevate.xml \
    /opt/solr/server/solr/configsets/$SOLR_CORE/conf/

ADD solrconfig.xml /opt/solr/server/solr/configsets/$SOLR_CORE/conf/managed-schema

ENV SOLR_USER solr




# Set ownership and permissions
RUN chown -R $SOLR_USER:$SOLR_USER /opt/solr/server/solr/configsets/$SOLR_CORE
RUN chmod -R 755 /opt/solr/server/solr/configsets/$SOLR_CORE



USER solr


# Create Core.properties
CMD ["sh", "-c", "solr-precreate ckan $SOLR_CONFIG_DIR/ckan"]


