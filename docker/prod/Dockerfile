ARG PHP_VERSION=X
FROM php:${PHP_VERSION}-fpm-stretch

ENV COMPOSER_ALLOW_SUPERUSER 1

RUN rm /etc/localtime ; \
    ln -s /usr/share/zoneinfo/Brazil/East /etc/localtime

RUN apt-get update -y && \
    apt-get install -y libpng-dev libicu-dev g++ libxml2-dev libssl-dev libzip-dev nginx && \
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
RUN echo "upload_max_filesize=2M" > /usr/local/etc/php/conf.d/99-ajustes.ini \
    && echo "post_max_size=8M" >> /usr/local/etc/php/conf.d/99-ajustes.ini \
    && echo "memory_limit=2G" >> /usr/local/etc/php/conf.d/99-ajustes.ini

# Copy cronfile file to the cron.d directory
COPY cronfile /etc/cron.d/cronfile
 
# Give execution rights on the cron job
RUN chmod 0644 /etc/cron.d/cronfile

# Apply cron job
RUN crontab /etc/cron.d/cronfile

WORKDIR /var/www/html/

ADD . /var/www/html/
#RUN rm -f *.conf Dockerfile /etc/nginx/sites-enabled/*

RUN curl -sS https://getcomposer.org/installer | php \
    && cp composer.phar /usr/local/bin/composer \
    && chmod +x /usr/local/bin/composer \
    # && composer self-update 1.10.17 \
    && composer update

RUN apt-get update \
    && apt-get install supervisor -y

RUN chown www-data:www-data -R * \
    && find . -type d -exec chmod 755 {} \; \
    && find . -type f -exec chmod 644 {} \;

#RUN echo "<?php echo phpinfo(); ?>" > /var/www/html/xyzinfo.php

RUN echo "Deploy: $(date '+%d/%m/%Y %H:%M:%S')" > /var/www/html/info.txt

COPY nginx.conf /etc/nginx/conf.d/
COPY cors.conf.inc /etc/nginx/conf.d/
RUN rm -f nginx.conf cors.conf.inc Dockerfile /etc/nginx/sites-enabled/*

EXPOSE 9000 80
RUN nginx -t

CMD cron & /usr/bin/python /usr/bin/supervisord -c laravel-worker.conf & nginx -g "daemon off;" & php-fpm