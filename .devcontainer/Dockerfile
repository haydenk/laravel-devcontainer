ARG PHP_VERSION=8.4
ARG XDEBUG_VERSION=3.4.5

ARG COMPOSER_HOME="/var/cache/composer"
ARG COMPOSER_VERSION=2.5.5

ARG PHPCSFIXER_INSTALL=true
ARG PHPCSFIXER_VERSION=3.84.0

ARG PHPCBF_INSTALL=true
ARG PHPCBF_VERSION=3.7.2

ARG TIMEZONE="America/Chicago"

FROM composer:${COMPOSER_VERSION} AS composer_base

FROM php:${PHP_VERSION}-fpm

ARG TIMEZONE
ENV TZ=${TIMEZONE}

ARG PHPCSFIXER_INSTALL
ARG PHPCSFIXER_VERSION

ARG COMPOSER_HOME
ENV COMPOSER_HOME=${COMPOSER_HOME}

RUN set -e -x \
    && mkdir -p "${COMPOSER_HOME}/cache/files" \
    && mkdir -p "${COMPOSER_HOME}/cache/vcs" \
    && chmod -R 777 "${COMPOSER_HOME}"

COPY --from=composer_base /usr/bin/composer /usr/bin/composer

ADD config/zz-overrides.ini /usr/local/etc/php/conf.d/zz-overrides.ini
ADD config/zz-docker.conf /usr/local/etc/php-fpm.d/zz-docker.conf

RUN set -e -x \
    && printf "\e[0;105m%s\e[0;0m\n" "Install Linux binaries" \
    && apt-get update -yqq \
    && apt-get install -y --no-install-recommends caddy curl libsqlite3-dev libpq-dev libpng-dev libzip-dev zip unzip \
    && docker-php-ext-install pdo pdo_pgsql pdo_sqlite gd zip \
    && pecl install xdebug-${XDEBUG_VERSION} && docker-php-ext-enable pdo_pgsql pdo_sqlite xdebug \
    && docker-php-source delete \
    && cp -v /usr/local/etc/php/php.ini-development /usr/local/etc/php/php.ini \
    && apt-get clean \
    && rm -rf /tmp/* /var/list/apt/*
