FROM php:8.4.10-fpm-bullseye

USER root

ENV DEBIAN_FRONTEND=noninteractive

RUN echo 'APT::Get::Assume-Yes "true";' > /etc/apt/apt.conf.d/90tugboat && \
    echo 'DPkg::Options "--force-confnew";' >> /etc/apt/apt.conf.d/90tugboat && \
    (grep Linux\ 7 /etc/issue && echo "deb http://http.debian.net/debian wheezy-backports main" > /etc/apt/sources.list.d/wheezy-backports.list || /bin/true) && \
    (apt-get update || /bin/true) && \
    apt-get install eatmydata && ln -sf /usr/bin/eatmydata /usr/local/bin/apt-get && \
    \
    apt-get install apt-transport-https && \
    apt-get update && \
    \
    mkdir -p /usr/share/man/man1 && \
    \
    apt-get install -y \
        git locales sudo openssh-client ca-certificates tar gzip build-essential \
        net-tools netcat-openbsd unzip zip bzip2 gnupg curl wget vim runit \
        rsync apt-transport-https jq && \
    \
    mkdir -p /etc/service && \
    \
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

ENV COMPOSER_NO_INTERACTION=1 \
    COMPOSER_DISCARD_CHANGES=1 \
    COMPOSER_ALLOW_SUPERUSER=1 \
    WP_CLI_ALLOW_ROOT=1 \
    DOCROOT=/usr/share/nginx/html

RUN apt-get install -y libfreetype6-dev libjpeg62-turbo-dev libmcrypt-dev libpng-dev libwebp-dev libmagickwand-dev nginx && \
    (apt-get install -y mysql-client || apt-get install -y mariadb-client) && \
    docker-php-ext-configure gd --with-jpeg --with-freetype --with-webp && \
    docker-php-ext-install -j$(nproc) gd iconv opcache pdo_mysql && \
    \
    (((pecl install xdebug || pecl install xdebug-2.5.5 || pecl install xdebug-2.8.0beta2) && \
    bash -c "(pecl install mcrypt <<< '' || pecl install mcrypt-1.0.1 <<< '' || docker-php-ext-install mcrypt || /bin/true)" && \
    (docker-php-ext-enable mcrypt || /bin/true)) || /bin/true) && \
    ((pecl install --configureoptions 'with-imagick="autodetect"' imagick && \
    docker-php-ext-enable imagick) || /bin/true) && \
    \
    php -r "copy('https://raw.githubusercontent.com/composer/getcomposer.org/master/web/installer', 'composer-setup.php');" && \
    php composer-setup.php && \
    php -r "unlink('composer-setup.php');" && \
    mv composer.phar /usr/local/bin/composer && \
    mv ${DOCROOT} ${DOCROOT}.original && \
    ln -s ${DOCROOT}.original ${DOCROOT} && \
    rm /etc/nginx/sites-enabled/default

# Configure php-fpm to run as a service.
COPY files/service/php-fpm/run /etc/service/php-fpm/run
COPY files/service/nginx/run /etc/service/nginx/run
COPY files/tugboat.ini /usr/local/etc/php/conf.d/tugboat.ini
COPY files/nginx.conf /etc/nginx/nginx.conf
COPY files/sites-enabled/* /etc/nginx/sites-enabled/

HEALTHCHECK CMD /bin/nc -z 127.0.0.1 80

RUN apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*



STOPSIGNAL SIGTERM

ENV RUNITDIR=/usr/bin/

COPY runit-init.sh /runit-init.sh

CMD ["/runit-init.sh"]
