FROM php:5.6-apache

ENV TZ=Asia/Taipei
# Set Server timezone.
RUN echo $TZ > /etc/timezone \
    && dpkg-reconfigure -f noninteractive tzdata \
    && echo date.timezone = $TZ > /usr/local/etc/php/conf.d/docker-php-ext-timezone.ini

RUN mkdir -p /etc/apache2/ssl

# Defaul config php.ini
COPY ./config/php.ini /usr/local/etc/php/
COPY ./index.php /var/www/html/

## Copy modified httpd.conf into container

# RUN apt-get -y update && apt-get -y upgrade
RUN apt-get update; \
    apt-get install -y libpq5 libpq-dev; \
    docker-php-ext-install pdo pdo_pgsql pgsql; \
    apt-get autoremove --purge -y libpq-dev; \
    apt-get clean ; \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/*

RUN apt-get -y update \
    && apt-get install -y vim \
    && apt-get install -y iputils-ping \
    && apt-get install -y --no-install-recommends \
    libmemcached11 \
    libmemcachedutil2 \
    libmemcached-dev \
    libz-dev \
    build-essential \
    apache2-utils \
    libmagickwand-dev \
    imagemagick \
    libcurl4-openssl-dev \
    libssl-dev \
    libc-client2007e-dev \
    libkrb5-dev \
    libmcrypt-dev \
    unixodbc-dev \
    && apt-get clean \
    && rm -r /var/lib/apt/lists/*

RUN apt-get -y update \
    && apt-get install -y \
        libfreetype6-dev \
        libmcrypt-dev \
        libjpeg-dev \
        libpng-dev
Run docker-php-ext-configure gd \
        --enable-gd-native-ttf \
        --with-freetype-dir=/usr/include/freetype2 \
        --with-png-dir=/usr/include \
        --with-jpeg-dir=/usr/include \
    && docker-php-ext-install gd \
    && docker-php-ext-install mbstring \
    && docker-php-ext-enable gd

# Config Extension 
RUN docker-php-ext-configure imap --with-imap-ssl --with-kerberos \
    && docker-php-ext-configure pdo_odbc --with-pdo-odbc=unixODBC,/usr

# Install Extension mysqli mysql mbstring opcache pdo_mysql gd mcrypt zip imap bcmath soap pdo
RUN docker-php-ext-install mysqli mysql opcache pdo_mysql mcrypt iconv zip imap soap pdo pdo_odbc


# Enable Apache mod_rewrite
RUN a2enmod rewrite ssl headers

# Memcache
RUN pecl install memcached-2.2.0 \
    && docker-php-ext-enable memcached

# Imagick
RUN pecl install imagick \
    && docker-php-ext-enable imagick
    
RUN chown -R www-data:www-data /var/www

EXPOSE 80
EXPOSE 443