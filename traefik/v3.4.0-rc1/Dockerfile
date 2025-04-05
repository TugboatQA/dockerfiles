FROM traefik:v3.4.0-rc1

USER root

RUN apk add --no-cache bash git openssh-client runit jq


RUN mkdir -p /etc/service/traefik
COPY run /etc/service/traefik/run

STOPSIGNAL SIGTERM

ENV RUNITDIR=

COPY runit-init.sh /runit-init.sh

CMD ["/runit-init.sh"]
