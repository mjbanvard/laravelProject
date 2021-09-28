FROM node:latest AS node
FROM php:7.4-fpm-alpine

# Copy composer.lock and composer.json
COPY --from=composer:latest /usr/bin/composer /usr/local/bin/composer
COPY composer.lock composer.json /var/www/

# Copy package.lock and package-lock.json
COPY package.json package-lock.json /var/www/

COPY --from=node /usr/local/lib/node_modules /usr/local/lib/node_modules
COPY --from=node /usr/local/bin/node /usr/local/bin/node

ADD https://github.com/mlocati/docker-php-extension-installer/releases/latest/download/install-php-extensions /usr/local/bin/

RUN chmod +x /usr/local/bin/install-php-extensions && sync && \
    install-php-extensions mbstring pdo_mysql zip exif pcntl gd

# RUN apt-get update && apt-get install -y libmcrypt-dev \
#     mysql-client libmagickwand-dev --no-install-recommends \
#     && pecl install imagick \
#     && docker-php-ext-enable imagick \
#     && docker-php-ext-install mcrypt pdo_mysql

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

WORKDIR /var/www

RUN apk update && apk add \
    build-base \
    freetype-dev \
    libjpeg-turbo-dev \
    libpng-dev \
    libzip-dev \
#    libfreetype6-dev \
#    libjpeg62-turbo-dev \
#    libonig-dev\
    zip \
    jpegoptim optipng pngquant gifsicle \
    vim \
    unzip \
    git \
    curl 
#    oniguruma

# RUN node install oniguruma

# RUN docker-php-ext-install pdo_mysql mbstring zip exif pcntl
# RUN docker-php-ext-configure gd --with-gd --with-freetype-dir=/usr/include/ \
#     --with-jpeg-dir=/usr/include/ --with-png-dir=/usr/include/
# RUN docker-php-ext-install gd

# Install Redis Extension
RUN apk add autoconf && pecl install -o -f redis \
    && rm -rf /tmp/pear \
    && docker-php-ext-enable redis && apk del autoconf

# Copy config
COPY ./config/php/local.ini /usr/local/etc/php/conf.d/local.ini

# Add user
RUN addgroup -g 1000 -S www && \
    adduser -u 1000 -S www -G www

USER www
USER root

COPY --chown=www:www . /var/www

EXPOSE 9000