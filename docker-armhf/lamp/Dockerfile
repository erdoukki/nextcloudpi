FROM ownyourbits/debian-ncp-armhf

MAINTAINER Ignacio Núñez Hernanz <nacho@ownyourbits.com>

SHELL ["/bin/bash", "-c"]

ENV DOCKERBUILD 1

COPY etc/library.sh lamp.sh /usr/local/etc/

RUN \

# installation
source /usr/local/etc/library.sh; \
set +x; \
install_script /usr/local/etc/lamp.sh; \

# stop mysqld
mysqladmin -u root shutdown; \

# mariaDB fixups (move database to /data-ro, which will be in a persistent volume)
mkdir -p /data-ro /data; \
mv /var/lib/mysql /data-ro/database; \
sed -i "s|^datadir.*|datadir = /data-ro/database|" /etc/mysql/mariadb.conf.d/90-ncp.cnf; \

# package cleanup 
apt-get autoremove -y; \
apt-get clean; \
find /var/lib/apt/lists -type f | xargs rm; \
rm -rf /usr/share/man/*; \
rm -rf /usr/share/doc/*; \
rm /var/cache/debconf/*-old; \
rm -f /var/log/alternatives.log /var/log/apt/*; \

# specific cleanup
rm /data-ro/database/ib_logfile*; \
rm /usr/local/etc/lamp.sh

COPY docker-common/lamp/010lamp /etc/services-enabled.d/

ENTRYPOINT ["/run-parts.sh"]

EXPOSE 80 443
