FROM my-local/app-php-base

ARG APP_ENV=prod
ENV APP_ENV=$APP_ENV

ARG COMPOSER_FLAGS="--no-dev --prefer-dist --optimize-autoloader --classmap-authoritative --no-interaction --no-progress --no-suggest --ansi"

COPY --chown=app:www-data composer.* ./
RUN composer install $COMPOSER_FLAGS --no-scripts --no-autoloader \
	&& mkdir var

COPY --chown=app:www-data public/index.php public/
COPY --chown=app:www-data bin/console bin/
COPY --chown=app:www-data app app
COPY --chown=app:www-data public/build/manifest.* app/build/
COPY --chown=app:www-data src src

ARG RELEASE_SHA
ENV RELEASE_VERSION=$RELEASE_SHA

RUN mv app/config/parameters.yaml.${APP_ENV} app/config/parameters.yaml \
	&& composer install ${COMPOSER_FLAGS} \
	&& bin/console cache:warmup