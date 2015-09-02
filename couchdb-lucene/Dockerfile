FROM docker.io/java:openjdk-7-jdk

MAINTAINER GreenLizard Labs <gabriel@greenlizard.com.br>

ENV COUCHDB_LUCENE_VERSION 1.0.2

RUN apt-get update \
 && DEBIAN_FRONTEND=noninteractive apt-get install -y python maven \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# RUN adduser --system --disabled-password \
#  --home /opt/couchdb-lucene-$COUCHDB_LUCENE_VERSION --no-create-home --shell=/bin/bash --group --gecos "" couchdb
# USER couchdb

# Get the source
RUN mkdir /building \
 && wget -qO- https://github.com/rnewson/couchdb-lucene/archive/v$COUCHDB_LUCENE_VERSION.tar.gz | tar xz -C /building \
 && cd /building/couchdb-lucene-$COUCHDB_LUCENE_VERSION \
 && mvn \
 && tar -C /opt -xvzf /building/couchdb-lucene-$COUCHDB_LUCENE_VERSION/target/couchdb-lucene-$COUCHDB_LUCENE_VERSION-dist.tar.gz \
 && mv /opt/couchdb-lucene-$COUCHDB_LUCENE_VERSION /opt/couchdb-lucene \
 && sed -e 's/^host=localhost$/host=0.0.0.0/' -i /opt/couchdb-lucene/conf/couchdb-lucene.ini \
 && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /building

ADD ./opt/start_couch_lucene /opt/couchdb-lucene/start_couch_lucene
WORKDIR /opt/couchdb-lucene
CMD ["/opt/couchdb-lucene/start_couch_lucene"]

EXPOSE 5985
VOLUME ['/opt/couchdb-lucene/indexes', '/opt/couchdb-lucene/logs']
