FROM node:latest AS node
FROM php:7.4-fpm-alpine

COPY --from=node /usr/local/lib/node_modules /usr/local/lib/node_modules
COPY --from=node /usr/local/bin/node /usr/local/bin/node

WORKDIR /var/www

RUN apk update && apk add \
    build-base \
    freetype-dev \
    libjpeg-turbo-dev \
    libpng-dev \
    libzip-dev \
#    libonig-dev\
    zip \
    jpegoptim optipng pngquant gifsicle \
    vim \
    unzip \
    git \
    curl 
#    oniguruma

# RUN node install oniguruma

RUN docker-php-ext-install pdo_mysql mbstring zip exif pcntl
RUN docker-php-ext-configure gd --with-gd --with-freetype-dir=/usr/include/ \
    --with-jpeg-dir=/usr/include/ --with-png-dir=/usr/include/
RUN docker-php-ext-install gd

# Copy config
COPY ./config/php/local.ini /usr/local/etc/php/conf.d/local.ini

# Add user
RUN addgroup -g 1000 -S www && \
    adduser -u 1000 -S www -G www

USER www

COPY --chown=www:www . /var/www

EXPOSE 9000