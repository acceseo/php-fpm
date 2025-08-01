FROM composer/composer:2.8.10-bin AS composer

FROM wordpress:cli-2.12.0 AS wp-cli

FROM php:7.4.33-fpm

LABEL org.opencontainers.image.title="PHP-FPM"
LABEL org.opencontainers.image.description="Opinionated PHP-FPM container for development"
LABEL org.opencontainers.image.authors="acceseo Projects S.L."
LABEL org.opencontainers.image.url="https://github.com/acceseo/php-fpm"
LABEL org.opencontainers.image.licenses="MIT License"

ENV PHP_UPLOAD_MAX_FILESIZE=32M
ENV PHP_POST_MAX_SIZE=32M
ENV PHP_MAX_INPUT_VARS=50000
ENV PHP_MEMORY_LIMIT=256M
ENV PHP_MAX_EXECUTION_TIME=30
ENV PHP_MAX_INPUT_TIME=60
ENV TZ=Europe/Madrid

ENV PHP_IDE_CONFIG="serverName=localhost"

ENV PHP_APP_DIRECTORY=/app

ENV COMPOSER_MEMORY_LIMIT=-1

WORKDIR ${PHP_APP_DIRECTORY}

RUN mkdir -p ${PHP_APP_DIRECTORY}

RUN apt-get update && apt-get install -y \
# php-ext gd
    libjpeg62-turbo-dev libpng-dev libwebp-dev\
# php-ext intl
    zlib1g-dev libicu-dev g++ \
# php-ext zip
    libzip-dev \
# composer
    zip \
# https://symfony.com/blog/fast-smart-flex-recipe-upgrades-with-recipes-update#hello-recipes-update
    git \
#php-ext pgsql
    libpq-dev

RUN docker-php-ext-configure gd --enable-gd --with-webp --with-jpeg \
    && docker-php-ext-configure exif \
    && docker-php-ext-configure intl \
    && docker-php-ext-install gd exif intl mysqli pdo_mysql zip pgsql pdo_pgsql

RUN pecl install xdebug-3.1.6 \
    && docker-php-ext-enable xdebug

COPY xdebug.ini $PHP_INI_DIR/conf.d/xdebug.ini
COPY php.ini $PHP_INI_DIR/php.ini

RUN usermod -u 1000 www-data
RUN chown www-data ~www-data

COPY --from=composer /composer /usr/bin/composer

COPY --from=wp-cli /usr/local/bin/wp /usr/local/bin/wp
