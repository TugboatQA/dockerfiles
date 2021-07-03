FROM traefik:v2.4.9

USER root

RUN apk add --no-cache bash git openssh-client runit


RUN mkdir -p /etc/service/traefik
COPY run /etc/service/traefik/run

STOPSIGNAL SIGTERM

CMD ["/sbin/runsvdir", "-P", "/etc/service"]
