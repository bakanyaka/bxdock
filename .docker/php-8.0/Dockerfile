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
    && apt-get install -y php8.0 php8.0-dev \
        php8.0-pgsql php8.0-sqlite3 php8.0-gd \
        php8.0-curl php8.0-memcached \
        php8.0-imap php8.0-mysql php8.0-mbstring \
        php8.0-xml php8.0-zip php8.0-bcmath php8.0-soap \
        php8.0-intl php8.0-readline php8.0-pcov \
        php8.0-msgpack php8.0-igbinary php8.0-ldap \
        php8.0-redis php8.0-swoole \
    && php -r "readfile('https://getcomposer.org/installer');" | php -- --install-dir=/usr/bin/ --filename=composer \
    && curl -sLS https://deb.nodesource.com/setup_$NODE_VERSION.x | bash - \
    && apt-get install -y nodejs \
    && npm install -g npm \
    && apt-get update \
    && apt-get install -y mysql-client

RUN apt-get update \
    && apt-get install -y apache2

RUN apt-get -y autoremove \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN update-alternatives --set php /usr/bin/php8.0

RUN setcap "cap_net_bind_service=+ep" /usr/bin/php8.0

# Copy and update root CA
COPY ca-certificates/* /usr/local/share/ca-certificates/
RUN update-ca-certificates

COPY php.ini /etc/php/8.0/cli/conf.d/99-php.ini
COPY php.ini /etc/php/8.0/apache2/conf.d/99-php.ini
COPY default.conf /etc/apache2/sites-available/000-default.conf

RUN groupmod -g $WWWGROUP www-data && usermod -u $WWWUSER www-data

EXPOSE 80 443

CMD apachectl -D FOREGROUND
