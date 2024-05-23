ARG PHP_VERSION=X
FROM php:${PHP_VERSION}-fpm-stretch

ENV COMPOSER_ALLOW_SUPERUSER 1

RUN rm /etc/localtime ; \
    ln -s /usr/share/zoneinfo/Brazil/East /etc/localtime

RUN apt-get update -y && \
    apt-get install -y libpng-dev libicu-dev g++ libxml2-dev libssl-dev libzip-dev && \
    apt-get -y autoclean && \
    rm -rf /var/lib/apt/lists/*

RUN docker-php-ext-install phar ; docker-php-ext-install gd intl bcmath pdo mbstring pdo_mysql soap mysqli opcache xml json zip iconv


# Install dependencies
RUN apt-get update \
  && apt-get upgrade -y \
  && apt-get install -y --no-install-recommends \
  apt-utils \
  cron \
  git \
  mariadb-client \
  nano \
  nodejs \
  python3 \
  python3-pip \
  redis-tools \
  sendmail-bin \
  sendmail \
  sudo \
  unzip \
  vim \
  libbz2-dev \
  libjpeg62-turbo-dev \
  libpng-dev \
  libfreetype6-dev \
  libgeoip-dev \
  wget \
  libgmp-dev \
  libmagickwand-dev \
  libmagickcore-dev \
  libc-client-dev \
  libkrb5-dev \
  libicu-dev \
  libldap2-dev \
  libpspell-dev \
  librecode0 \
  librecode-dev \
  libssh2-1 \
  libssh2-1-dev \
  libtidy-dev \
  libxslt1-dev \
  libjpeg-dev \
  libyaml-dev \
  libzip-dev \
  zip \
  unzip \
  && rm -rf /var/lib/apt/lists/*


# Configure the gd library
RUN docker-php-ext-configure \
  gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/
RUN docker-php-ext-configure \
  imap --with-kerberos --with-imap-ssl
RUN docker-php-ext-configure \
  ldap --with-libdir=lib/x86_64-linux-gnu
RUN docker-php-ext-configure \
  opcache --enable-opcache
RUN docker-php-ext-configure \
  zip --with-libzip


# Install required PHP extensions
RUN docker-php-ext-install -j$(nproc) \
  bcmath \
  bz2 \
  calendar \
  exif \
  gd \
  gettext \
  gmp \
  imap \
  intl \
  ldap \
  mysqli \
  opcache \
  pdo_mysql \
  pspell \
  recode \
  shmop \
  soap \
  sockets \
  sysvmsg \
  sysvsem \
  sysvshm \
  tidy \
  xmlrpc \
  xsl \
  zip \
  pcntl

RUN pecl install -o -f \
  igbinary \
  imagick \
  mailparse \
  msgpack \
  oauth \
  pcov \
  propro \
  raphf \
  redis \
  yaml


RUN cd /tmp \
    && git clone https://git.php.net/repository/pecl/networking/ssh2.git \
    && cd /tmp/ssh2/ \
    && .travis/build.sh \
    && docker-php-ext-enable ssh2

RUN docker-php-ext-enable \
  bcmath \
  bz2 \
  calendar \
  exif \
  gd \
  gettext \
  gmp \
  igbinary \
  imagick \
  imap \
  intl \
  ldap \
  mailparse \
  msgpack \
  mysqli \
  oauth \
  opcache \
  pcov \
  pdo_mysql \
  propro \
  pspell \
  raphf \
  recode \
  redis \
  shmop \
  soap \
  sockets \
  ssh2 \
  sysvmsg \
  sysvsem \
  sysvshm \
  tidy \
  xmlrpc \
  xsl \
  yaml \
  zip \
  pcntl


#NEWRELIC
#ADD ./newrelic.ini /usr/local/etc/php/conf.d/98-newrelic.ini
#RUN echo "deb http://apt.newrelic.com/debian/ newrelic non-free" > /etc/apt/sources.list.d/newrelic.list
#RUN apt-get update -y \
#    && apt-get install newrelic-php5 -y --allow-unauthenticated


WORKDIR /tmp/
#COPY configs .
#RUN mv url-services.conf /etc/nginx/conf.d/url-services.conf

#Ajusta upload max file e post_max_size
RUN echo "upload_max_filesize=4m" >> /usr/local/etc/php/conf.d/99-ajustes.ini \
    && echo "post_max_size=10m" >> /usr/local/etc/php/conf.d/99-ajustes.ini \
    && echo "memory_limit=512m" >> /usr/local/etc/php/conf.d/99-ajustes.ini
    
WORKDIR /var/www/html/

RUN curl -sS https://getcomposer.org/installer | php \
    && mv composer.phar /usr/local/bin/composer \
    && chmod +x /usr/local/bin/composer
    # && composer update

#RUN rm /etc/apt/preferences.d/no-debian-php
#RUN wget https://xdebug.org/files/xdebug-2.9.8.tgz \
#    && tar -xzf xdebug-2.9.8.tgz \
#    && cd xdebug-2.9.8 \
#    && phpize \
#    && ./configure --enable-xdebug \
#    && make \
#    && make install

# install xdebug
RUN set -xe && \
        docker-php-source extract && \
        pecl install xdebug-2.9.8 && \
        docker-php-ext-enable xdebug && \
        docker-php-source delete

RUN echo "xdebug.default_enable=1" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.remote_enable=1" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.remote_connect_back=0" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.remote_port=9002" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
#    && echo "xdebug.scream=0" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
#    && echo "xdebug.cli_color=1" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
#    && echo "xdebug.show_local_vars=1" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.remote_autostart=1" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.remote_host=host.docker.internal" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.idekey=VSCODE" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.remote_handler=dbgp" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
#    && echo "xdebug.profiler_enable=0" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
#    && echo "xdebug.remote_mode=req" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "/usr/local/etc/php/xdebug.log" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini

#RUN chown www-data:www-data -R * \
#    && find . -type d -exec chmod 755 {} \; \
#    && find . -type f -exec chmod 644 {} \;

#RUN echo "<?php echo phpinfo(); ?>" > /var/www/html/xyzinfo.php

RUN echo "Deploy: $(date '+%d/%m/%Y %H:%M:%S')" > /var/www/html/info.txt


#EXPOSE 9001

CMD php-fpm