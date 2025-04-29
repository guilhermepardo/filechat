FROM php:8.3-fpm

WORKDIR /var/www/html

RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential autoconf pkg-config \
    libmemcached-dev libz-dev libzip-dev libpq-dev libjpeg-dev libpng-dev libfreetype6-dev libssl-dev libxml2-dev

ENV PHP_EXTS="pdo_mysql pcntl bcmath opcache mysqli zip"
RUN docker-php-ext-install $PHP_EXTS

RUN docker-php-ext-configure zip
RUN docker-php-ext-install zip

RUN pecl channel-update pecl.php.net

ENV PECL_EXTS="inotify redis xdebug"
RUN pecl install $PECL_EXTS && docker-php-ext-enable $PECL_EXTS && pecl clear-cache

COPY conf.d /usr/local/etc/php/conf.d
COPY php.ini /usr/local/etc/php/php.ini

COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf

RUN apt-get install -y --no-install-recommends \
    nginx gnupg2 sudo cron curl zip unzip software-properties-common supervisor sqlite3

RUN apt-get clean && \
	rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
	rm /var/log/lastlog /var/log/faillog

RUN usermod -u 1000 www-data

RUN mkdir -p /var/www/html/storage/logs/nginx && \
    chown -R www-data:www-data /var/www/html/storage

COPY --chown=www-data:www-data nginx.conf /etc/nginx/nginx.conf

COPY --from=composer:2.8.8 /usr/bin/composer /usr/local/bin/composer

EXPOSE 80

CMD ["/usr/bin/supervisord"]