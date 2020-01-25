FROM alpine:3.9.5

USER root

RUN apk add --no-cache bash git openssh-client runit




CMD ["/sbin/runsvdir", "-P", "/etc/service"]