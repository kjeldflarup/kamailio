FROM ubuntu:18.04

RUN apt update
RUN apt install -y build-essential git bison flex pkg-config libmysqlclient-dev libssl-dev libpcre3-dev mysql-client


RUN mkdir -p /usr/local/src/kamailio-devel && \
  cd /usr/local/src/kamailio-devel && \
  git clone --depth 1 -b 5.3.1 https://github.com/kamailio/kamailio kamailio
RUN cd /usr/local/src/kamailio-devel/kamailio && \
  make cfg && \
  make include_modules="db_mysql dialplan" cfg && \
  make all && \
  make install

# Assume that /usr/local/etc/kamailio is mapped as a volume
# Keep the original, and copy it back if not mapped
RUN mv /usr/local/etc/kamailio /usr/local/etc/kamailio.org ;\
echo '#!/bin/sh' > /entrypoint.sh && \
echo 'if [ ! -d /usr/local/etc/kamailio ] ; then \
  echo No /usr/local/etc/kamailio mapped; \
  exit 1;\
fi; \
if [ ! -e /usr/local/etc/kamailio/kamailio.cfg ] ; then \
  cp -p /usr/local/etc/kamailio.org/kamailio.cfg /usr/local/etc/kamailio/kamailio.cfg ;\
fi; \
if [ ! -e /usr/local/etc/kamailio/kamctlrc ] ; then \
  cp -p /usr/local/etc/kamailio.org/kamctlrc /usr/local/etc/kamailio/kamctlrc ; \
fi; \
exec "$@"' >> /entrypoint.sh ;\
chmod +x /entrypoint.sh

ENTRYPOINT [ "/entrypoint.sh" ]

RUN rm -f /etc/localtime; ln -s /usr/share/zoneinfo/Europe/Copenhagen /etc/localtime

#RUN apk add git fail2ban mbuffer tcpdump
#RUN apk add -y python3-pip
#RUN pip3 install scp


# And run in the foreground (-D). 
CMD /usr/local/sbin/kamailio -D -P /var/run/kamailio/kamailio.pid -m 128 -M 12 -f /usr/local/etc/kamailio/kamailio.cfg

