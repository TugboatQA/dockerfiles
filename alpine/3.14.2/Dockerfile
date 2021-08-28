FROM alpine:3.14.2

USER root

RUN apk add --no-cache bash git openssh-client runit




STOPSIGNAL SIGTERM

CMD ["/sbin/runsvdir", "-P", "/etc/service"]
