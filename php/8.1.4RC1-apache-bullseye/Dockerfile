FROM php:8.1.4RC1-apache-bullseye

USER root

ENV DEBIAN_FRONTEND=noninteractive

RUN echo 'APT::Get::Assume-Yes "true";' > /etc/apt/apt.conf.d/90tugboat && \
    echo 'DPkg::Options "--force-confnew";' >> /etc/apt/apt.conf.d/90tugboat && \
    (grep Linux\ 7 /etc/issue && echo "deb http://http.debian.net/debian wheezy-backports main" > /etc/apt/sources.list.d/wheezy-backports.list || /bin/true) && \
    (apt-get update || /bin/true) && \
    \
    apt-get install apt-transport-https && \
    apt-get update && \
    \
    mkdir -p /usr/share/man/man1 && \
    mkdir -p /etc/service && \
    mkdir -p /usr/share/tugboat && \
    \
    apt-get install -y \
        git locales sudo openssh-client ca-certificates tar gzip build-essential \
        net-tools netcat-openbsd unzip zip bzip2 gnupg curl wget vim runit \
        rsync apt-transport-https && \
    (apt-get install -y ssmtp || apt-get install -y msmtp-mta) && \
    (grep Linux\ 7 /etc/issue && apt-get -t wheezy-backports install -y git || /bin/true) && \
    \
    echo "[user]" > ~/.gitconfig && \
    echo "    email = root@tugboat.qa" >> ~/.gitconfig && \
    echo "    name = Tugboat" >> ~/.gitconfig && \
    \
    ln -sf /usr/share/zoneinfo/Etc/UTC /etc/localtime && \
    \
    localedef -i en_US -c -f UTF-8 en_US.UTF-8 && \
    echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && \
    touch /usr/share/locale/locale.alias && \
    locale-gen

# Configure locale
ENV LANG=en_US.UTF-8 \
    LANGUAGE=en_US:en \
    LC_ALL=en_US.UTF-8

COPY share/README.txt /usr/share/tugboat/
COPY share/Makefile /usr/share/tugboat/

ENV COMPOSER_NO_INTERACTION=1 \
    COMPOSER_DISCARD_CHANGES=1 \
    COMPOSER_ALLOW_SUPERUSER=1 \
    DOCROOT=/var/www/html

RUN apt-get install -y libfreetype6-dev libjpeg62-turbo-dev libmcrypt-dev libpng-dev && \
    (apt-get install -y mysql-client || apt-get install -y mariadb-client) && \
    \
    (docker-php-ext-configure gd --with-freetype-dir=/usr/include/ && \
        docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ || \
        docker-php-ext-configure gd --with-freetype=/usr/include/ --with-jpeg=/usr/include/ || \
        docker-php-ext-configure gd --with-jpeg-dir=/usr/include/ || \
        docker-php-ext-configure gd --with-jpeg=/usr/include/) && \
    docker-php-ext-install -j$(nproc) gd iconv pdo_mysql && \
    \
    (((pecl install xdebug || pecl install xdebug-2.5.5 || pecl install xdebug-2.8.0beta2) && \
    bash -c "(pecl install mcrypt <<< '' || pecl install mcrypt-1.0.1 <<< '' || docker-php-ext-install mcrypt || /bin/true)" && \
    (docker-php-ext-enable mcrypt || /bin/true)) || /bin/true) && \
    \
    php -r "copy('https://raw.githubusercontent.com/composer/getcomposer.org/master/web/installer', 'composer-setup.php');" && \
    php composer-setup.php && \
    php -r "unlink('composer-setup.php');" && \
    \
    mv composer.phar /usr/local/bin/composer && \
    mkdir -p /etc/service/apache && \
    echo "umask 0002" >> /etc/apache2/envvars && \
    \
    mv ${DOCROOT} ${DOCROOT}.original && \
    ln -s ${DOCROOT}.original ${DOCROOT}

COPY files/apache.runit /etc/service/apache/run
COPY files/000-default.conf /etc/apache2/sites-available/000-default.conf
COPY files/tugboat.ini /usr/local/etc/php/conf.d/tugboat.ini

HEALTHCHECK CMD /bin/nc -z 127.0.0.1 80

RUN apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*



STOPSIGNAL SIGTERM

CMD ["/usr/bin/runsvdir", "-P", "/etc/service"]
