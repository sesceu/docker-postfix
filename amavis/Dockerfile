FROM ubuntu:latest
MAINTAINER Sebastian Schneider <mail@sesc.eu>

ENV DEBIAN_FRONTEND noninteractive

# Prerequisites
RUN echo "will.be.overwritten.com" > /etc/mailname \
    && apt-get update && apt-get install -y --no-install-recommends \
       rsyslog \
       amavisd-new \
       spamassassin \
       libnet-dns-perl \
       libmail-spf-perl \
       locales \
       pyzor \
       razor \
       netcat \
       jq \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN locale-gen en_US en_US.UTF-8 && dpkg-reconfigure locales

# Set up razor and pyzor
RUN su - amavis -s /bin/bash && razor-admin -create && razor-admin -register && pyzor discover

COPY ./config/conf.d /etc/amavis/conf.d
COPY ./config/rsyslog.conf /etc/rsyslog.conf
COPY ./config/whitelist /etc/amavis/whitelist

# Nice place for your settings
VOLUME ["/mail_settings"]

# Copy boot scripts
COPY ./boot.sh /
COPY ./boot.d /boot.d
RUN chmod 755 /boot.sh /boot.d/*

ENV AMAVIS=true

EXPOSE 10024
CMD ./boot.sh; amavisd-new; rsyslogd -n
HEALTHCHECK CMD nc -z localhost 10024 || exit 1
