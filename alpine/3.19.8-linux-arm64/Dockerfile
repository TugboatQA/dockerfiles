FROM alpine:3.19.8

USER root

RUN apk add --no-cache bash git openssh-client runit jq




STOPSIGNAL SIGTERM

ENV RUNITDIR=

COPY runit-init.sh /runit-init.sh

CMD ["/runit-init.sh"]
