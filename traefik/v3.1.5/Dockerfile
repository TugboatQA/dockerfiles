FROM traefik:v3.1.5

USER root

RUN apk add --no-cache bash git openssh-client runit jq


RUN mkdir -p /etc/service/traefik
COPY run /etc/service/traefik/run

STOPSIGNAL SIGTERM

CMD ["/sbin/runsvdir", "-P", "/etc/service"]
