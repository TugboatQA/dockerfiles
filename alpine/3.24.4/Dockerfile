# syntax=docker/dockerfile:1.29-labs
FROM --reset=volume,entrypoint alpine:3.24.4

USER root

RUN apk add --no-cache bash git openssh-client runit jq




STOPSIGNAL SIGTERM

ENV RUNITDIR=

COPY runit-init.sh /runit-init.sh

CMD ["/runit-init.sh"]
