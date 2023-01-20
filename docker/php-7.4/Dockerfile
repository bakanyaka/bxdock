FROM ubuntu:22.04

LABEL maintainer="Dmitriy Belyakov"

ARG WWWGROUP=1000
ARG WWWUSER=1000
ARG NODE_VERSION=16

WORKDIR /var/www/html

ENV DEBIAN_FRONTEND noninteractive
ENV TZ=UTC

RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN apt-get update \
    && apt-get install -y gnupg gosu curl ca-certificates zip unzip git supervisor sqlite3 libcap2-bin libpng-dev python2 dnsutils \
    && curl -sS 'https://keyserver.ubuntu.com/pks/lookup?op=get&search=0x14aa40ec0831756756d7f66c4f4ea0aae5267a6c' | gpg --dearmor | tee /usr/share/keyrings/ppa_ondrej_php.gpg > /dev/null \
    && echo "deb [signed-by=/usr/share/keyrings/ppa_ondrej_php.gpg] https://ppa.launchpadcontent.net/ondrej/php/ubuntu jammy main" > /etc/apt/sources.list.d/ppa_ondrej_php.list \
    && apt-get update \
    && apt-get install -y php7.4-fpm php7.4-cli php7.4-dev \
    php7.4-sqlite3 php7.4-gd \
    php7.4-curl php7.4-memcached \
    php7.4-imap php7.4-mysql php7.4-mbstring php7.4-opcache \
    php7.4-xml php7.4-json php7.4-zip php7.4-bcmath php7.4-soap \
    php7.4-intl php7.4-readline php7.4-pcov \
    php7.4-msgpack php7.4-igbinary php7.4-ldap \
    && php -r "readfile('https://getcomposer.org/installer');" | php -- --install-dir=/usr/bin/ --filename=composer \
    && curl -sLS https://deb.nodesource.com/setup_$NODE_VERSION.x | bash - \
    && apt-get install -y nodejs \
    && npm install -g npm \
    && apt-get update \
    && apt-get install -y mysql-client

RUN apt-get -y autoremove \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN setcap "cap_net_bind_service=+ep" /usr/bin/php7.4

RUN groupmod -g $WWWGROUP www-data && usermod -u $WWWUSER www-data

STOPSIGNAL SIGQUIT

COPY overrides.conf /etc/php/7.4/fpm/pool.d/z-overrides.conf
COPY php.ini /etc/php/7.4/fpm/conf.d/90-php.ini
COPY php.ini /etc/php/7.4/cli/conf.d/90-php.ini

WORKDIR "/var/www"

CMD ["/usr/sbin/php-fpm7.4", "-O" ]

EXPOSE 9000
