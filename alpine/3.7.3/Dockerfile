FROM alpine:3.7.3

USER root

RUN apk add --no-cache bash git openssh-client runit




CMD ["/sbin/runsvdir", "-P", "/etc/service"]