FROM alpine:3.10.6

USER root

RUN apk add --no-cache bash git openssh-client runit




STOPSIGNAL SIGTERM

CMD ["/sbin/runsvdir", "-P", "/etc/service"]
