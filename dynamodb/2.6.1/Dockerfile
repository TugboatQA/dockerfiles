FROM amazon/dynamodb-local:2.6.1

USER root

RUN yum install -y git sudo openssh-clients ca-certificates net-tools bzip2 \
      vim-enhanced nmap-ncat make gcc gcc-c++ rsync wget tar jq && \
    \
    ( yum install -y glibc-static || \
      yum --enablerepo=powertools install -y glibc-static || \
      ( \
        yum config-manager --set-enabled powertools && \
        yum install -y glibc-static \
      ) || \
      ( \
        yum config-manager --set-enabled $(yum repolist all | grep codeready | awk '{print $1}') && \
        yum install -y glibc-static \
      ) \
    ) && \
    \
    echo "[user]" > ~/.gitconfig && \
    echo "    email = root@tugboat.qa" >> ~/.gitconfig && \
    echo "    name = Tugboat" >> ~/.gitconfig && \
    ln -sf /usr/share/zoneinfo/Etc/UTC /etc/localtime && \
    \
    mkdir -p /etc/service && \
    ln -s /etc/service /service && \
    mkdir /package && \
    cd /package && \
    wget http://smarden.org/runit/runit-2.1.2.tar.gz && \
    tar -zxf runit-2.1.2.tar.gz && \
    rm runit-2.1.2.tar.gz && \
    cd admin/runit-2.1.2 && \
    package/install

# Configure locale
ENV LANG=en_US.UTF-8 \
    LANGUAGE=en_US:en \
    LC_ALL=en_US.UTF-8

RUN mkdir -p "/home/dynamodblocal/data"

HEALTHCHECK CMD /bin/nc -z 127.0.0.1 8000

RUN yum clean all -y && \
    rm -rf /tmp/* /var/tmp/*

RUN mkdir -p /etc/service/dynamodb
COPY run /etc/service/dynamodb/run

STOPSIGNAL SIGTERM

ENV RUNITDIR=/usr/local/bin/

COPY runit-init.sh /runit-init.sh

CMD ["/runit-init.sh"]
