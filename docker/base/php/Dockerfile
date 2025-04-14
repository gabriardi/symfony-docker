FROM php:8.3.20-fpm-alpine3.20
RUN apk add --no-cache \
	git \
	icu-dev \
	libmemcached-libs \
	zlib-dev \
	&& apk add --no-cache --virtual .ext-build-deps \
	autoconf \
	build-base \
	libmemcached-dev \
	libzip-dev \
	openssl-dev \
	cyrus-sasl-dev \
	pkgconfig \
	&& pecl install -o \
	memcached-3.3.0 \
	&& docker-php-ext-enable \
	memcached \
	&& docker-php-ext-install -j5 \
	intl \
	opcache \
	pdo_mysql \
	zip \
	&& apk del .ext-build-deps

WORKDIR /var/www/app
RUN adduser -D -u 501 app -G www-data \
	&& mkdir -p /home/app \
	&& chown -R app /home/app \
	&& chown -R app /var/www/app \
	## Composer
	&& curl -sS https://getcomposer.org/installer | php -- \
	--install-dir=/usr/local/bin --filename=composer \
	--version 2.8.8
USER app
# ARG $GITHUB_TOKEN
# RUN composer config -g github-oauth.github.com $GITHUB_TOKEN \
RUN composer global require \
	--no-interaction \
	&& rm -rf ~/.composer/cache