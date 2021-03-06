FROM docker.elastic.co/elasticsearch/elasticsearch:5.4.3

USER root

RUN yum install -y git sudo openssh-clients ca-certificates net-tools bzip2 vim-enhanced \
        nmap-ncat make gcc gcc-c++ rsync wget tar && \
    \
    (yum install -y glibc-static || yum --enablerepo=powertools install -y glibc-static) && \
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

COPY share/README.txt /usr/share/tugboat/
COPY share/Makefile /usr/share/tugboat/

ENV discovery.type single-node
HEALTHCHECK CMD /bin/nc -z 127.0.0.1 9200

RUN yum clean all -y && \
    rm -rf /tmp/* /var/tmp/*

RUN mkdir -p /etc/service/elasticsearch
COPY run /etc/service/elasticsearch/run

STOPSIGNAL SIGTERM

CMD ["/usr/local/bin/runsvdir", "-P", "/etc/service"]
