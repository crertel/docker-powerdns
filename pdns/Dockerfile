FROM debian:buster

MAINTAINER Fabio Rauber <fabiorauber@gmail.com>

ENV PDNSCONF_LAUNCH="gmysql" \
    PDNSCONF_GMYSQL_HOST="mysql" \
    PDNSCONF_GMYSQL_USER='pdns' \
    PDNSCONF_GMYSQL_DBNAME='pdns' \
    PDNSCONF_GMYSQL_PASSWORD='pdnspw' \
    PDNSCONF_INCLUDE_DIR="/etc/powerdns/pdns.d" \
    PDNSCONF_GMYSQL_DNSSEC="yes" \
    PDNSCONF_API_KEY="secret_api_key" \
    SECALLZONES_CRONJOB="no"

RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -q -y curl gnupg && \
    curl https://repo.powerdns.com/FD380FBB-pub.asc | apt-key add - 

ADD pdns.list /etc/apt/sources.list.d/pdns.list
ADD pdns.preference /etc/apt/preferences.d/pdns 

RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -q -y pdns-server pdns-backend-mysql mariadb-client && \
    rm /etc/powerdns/pdns.d/*.conf && rm /etc/powerdns/*.conf && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends cron jq && \
    rm /etc/cron.daily/* && \
    mkdir /var/run/pdns && \
    apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

EXPOSE 53/udp 53/tcp

ADD start.sh /usr/local/bin/start.sh
ADD fixdsrrs.sh /usr/local/bin/fixdsrrs.sh
ADD secallzones.sh /usr/local/bin/secallzones.sh
ADD schema.mysql.sql /usr/share/doc/pdns-backend-mysql/
RUN chmod a+x /usr/local/bin/*.sh

CMD ["/usr/local/bin/start.sh"]
