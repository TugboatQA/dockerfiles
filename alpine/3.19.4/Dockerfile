FROM alpine:3.19.4

USER root

RUN apk add --no-cache bash git openssh-client runit jq




STOPSIGNAL SIGTERM

CMD ["/sbin/runsvdir", "-P", "/etc/service"]
