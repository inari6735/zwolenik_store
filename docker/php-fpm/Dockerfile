FROM php:8.2-fpm-alpine

## APCU && OPCACHE
RUN apk add --update --no-cache --virtual .build-dependencies $PHPIZE_DEPS \
        && pecl install apcu \
        && docker-php-ext-enable apcu
RUN docker-php-ext-install opcache

## INTL
RUN apk add icu-dev libzip-dev --virtual .extensions-dev --no-cache
RUN apk add icu icu-data-full libzip --no-cache

RUN docker-php-ext-configure intl
RUN docker-php-ext-install -j$(nproc) intl

## MYSQL
RUN docker-php-ext-install mysqli \
      pdo \
      pdo_mysql

## LOCK
RUN docker-php-ext-install -j$(nproc) sysvsem \
      zip
RUN docker-php-ext-install sysvsem

## Images GD
RUN apk add --update --no-cache --virtual .image-dev freetype-dev \
        libpng-dev \
        jpeg-dev \
        libjpeg-turbo-dev

RUN apk add --update --no-cache libjpeg

RUN docker-php-ext-configure gd --with-jpeg=/usr/include/ && \
    docker-php-ext-install gd

## IMAGES IMAGICK
RUN apk add --update --no-cache --virtual .imagic-dev imagemagick-dev libwebp-dev
RUN apk add --update --no-cache imagemagick libgomp libwebp

RUN pecl install imagick
RUN docker-php-ext-enable imagick
RUN docker-php-ext-install -j$(nproc) exif

## SOAP
RUN apk add --update --no-cache --virtual .soap-dev \
        libxml2-dev \
        libxml2
RUN docker-php-ext-install xml soap
RUN docker-php-ext-enable soap

## REDIS
RUN pecl install redis && docker-php-ext-enable redis

### CLEAN
RUN pecl clear-cache \
        && apk del .build-dependencies \
        .imagic-dev \
        .image-dev \
        .soap-dev \
        .extensions-dev

COPY ./docker/php-fpm/opcache.ini /usr/local/etc/php/conf.d/opcache.ini
COPY ./docker/php-fpm/app.ini /usr/local/etc/php/conf.d/app.ini

WORKDIR /var/www

COPY . /var/www
RUN mkdir -p var/cache
RUN mkdir -p var/log
RUN chown www-data -R var/cache
RUN chown www-data -R var/log

RUN apk add bash

CMD ["php-fpm"]

EXPOSE 9000
