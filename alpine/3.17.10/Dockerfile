FROM alpine:3.17.10

USER root

RUN apk add --no-cache bash git openssh-client runit jq




STOPSIGNAL SIGTERM

CMD ["/sbin/runsvdir", "-P", "/etc/service"]
