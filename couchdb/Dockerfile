FROM greenlizard/base

MAINTAINER GreenLizard Labs <gabriel@greenlizard.com.br>

# Install instructions from https://cwiki.apache.org/confluence/display/COUCHDB/Debian

ENV COUCHDB_VERSION 1.6.1

RUN groupadd -r couchdb && useradd -d /var/lib/couchdb -g couchdb couchdb

# download dependencies, compile and install couchdb
RUN apt-get update -y \
  && apt-get install -y --no-install-recommends \
    build-essential ca-certificates curl \
    libmozjs185-dev libmozjs185-1.0 libnspr4 libnspr4-0d libnspr4-dev libcurl4-openssl-dev libicu-dev \
  && curl -ssL https://packages.erlang-solutions.com/erlang-solutions_1.0_all.deb -o esl.deb && dpkg -i esl.deb && apt-get update \
  && apt-get install -y --no-install-recommends erlang-nox=1:17.5 erlang-dev=1:17.5 \
  && curl -sSL http://apache.openmirror.de/couchdb/source/$COUCHDB_VERSION/apache-couchdb-$COUCHDB_VERSION.tar.gz -o couchdb.tar.gz \
  && curl -sSL https://www.apache.org/dist/couchdb/source/$COUCHDB_VERSION/apache-couchdb-$COUCHDB_VERSION.tar.gz.asc -o couchdb.tar.gz.asc \
  && curl -sSL https://www.apache.org/dist/couchdb/KEYS -o KEYS \
  && gpg --import KEYS && gpg --verify couchdb.tar.gz.asc \
  && mkdir -p /usr/src/couchdb \
  && tar -xzf couchdb.tar.gz -C /usr/src/couchdb --strip-components=1 \
  && cd /usr/src/couchdb \
  && ./configure --with-js-lib=/usr/lib --with-js-include=/usr/include/mozjs \
  && make && make install \
  && apt-get purge -y perl binutils cpp make build-essential libnspr4-dev libcurl4-openssl-dev libicu-dev \
  && apt-get autoremove -y \
  && apt-get update && apt-get install -y libicu48 --no-install-recommends \
  && rm -rf /var/lib/apt/lists/* /usr/src/couchdb /couchdb.tar.gz* /KEYS /esl.deb

# permissions
RUN chown -R couchdb:couchdb \
    /usr/local/lib/couchdb /usr/local/etc/couchdb \
    /usr/local/var/lib/couchdb /usr/local/var/log/couchdb /usr/local/var/run/couchdb \
  && chmod -R g+rw \
    /usr/local/lib/couchdb /usr/local/etc/couchdb \
    /usr/local/var/lib/couchdb /usr/local/var/log/couchdb /usr/local/var/run/couchdb \
  && mkdir -p /var/lib/couchdb

# Expose to the outside
RUN sed -e 's/^bind_address = .*$/bind_address = 0.0.0.0/' -i /usr/local/etc/couchdb/default.ini

COPY ./docker-entrypoint.sh /entrypoint.sh
COPY ./geocouch.sh /geocouch.sh

#RUN geocouch.sh

# Define mountable directories.
VOLUME ["/usr/local/var/log/couchdb", "/usr/local/var/lib/couchdb"]

EXPOSE 5984
WORKDIR /var/lib/couchdb

ENTRYPOINT ["/entrypoint.sh"]
CMD ["couchdb"]
