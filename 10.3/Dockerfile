FROM alpine:edge

LABEL maintainer="Ammar K."

COPY docker-entrypoint.sh /usr/local/bin/

RUN set -x \
    && addgroup -S mysql \
    && adduser -Sg mysql mysql \
    && apk add -u --no-cache apk-tools \
    && apk update \
    && apk add -u --no-cache \
        bash \
        mariadb \
        mariadb-client \
        pwgen \
        su-exec \
        tzdata \
    && sed -ri 's/^user\s/#&/' /etc/my.cnf /etc/my.cnf.d/* \
    # purge and re-create /var/lib/mysql with appropriate ownership
    && rm -rf /var/lib/mysql \
    && mkdir -p /var/lib/mysql /var/run/mysqld /run/mysqld \
    && chown -R mysql:mysql /var/lib/mysql /var/run/mysqld /run/mysqld \
    # ensure that /var/run/mysqld (used for socket and lock files) is writable regardless of the UID our mysqld instance ends up having at runtime
    && chmod 777 /var/run/mysqld /run/mysqld \
    && mkdir /docker-entrypoint-initdb.d \
    # comment out a few problematic configuration values
    && sed -Ei 's/^(bind-address|log)/#&/' /etc/my.cnf /etc/my.cnf.d/* \
    && ln -s usr/local/bin/docker-entrypoint.sh / # backward compatibility

COPY conf.d/* /etc/my.cnf.d/

VOLUME /var/lib/mysql

ENTRYPOINT ["docker-entrypoint.sh"]

EXPOSE 3306
CMD ["mysqld"]
