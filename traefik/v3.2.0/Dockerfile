FROM traefik:v3.2.0

USER root

RUN apk add --no-cache bash git openssh-client runit jq


RUN mkdir -p /etc/service/traefik
COPY run /etc/service/traefik/run

STOPSIGNAL SIGTERM

ENV RUNITDIR=/sbin

COPY runit-init.sh /runit-init.sh

CMD ["/runit-init.sh"]
