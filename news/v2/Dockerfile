FROM alpine:3.7

# Create user
RUN adduser -D -u 1000 -g 1000 -s /bin/sh www-data && \
    mkdir -p /www && \
    chown -R www-data:www-data /www

RUN apk add --no-cache --update tini

# Install a golang port of supervisord
COPY --from=ochinchina/supervisord:latest /usr/local/bin/supervisord /usr/bin/supervisord

# Install nginx & gettext (envsubst)
# Create cachedir and fix permissions
RUN apk add --no-cache --update \
    gettext \
    nginx && \
    mkdir -p /var/cache/nginx && \
    chown -R www-data:www-data /var/cache/nginx && \
    chown -R www-data:www-data /var/lib/nginx && \
    chown -R www-data:www-data /var/tmp/nginx

# Install PHP/FPM + Modules
RUN apk add --no-cache --update \
    bash \
    curl \
    git \
    openssl \
    php7 \
    php7-apcu \
    php7-bcmath \
    php7-bz2 \
    php7-cgi \
    php7-ctype \
    php7-curl \
    php7-dom \
    php7-fpm \
    php7-ftp \
    php7-gd \
    php7-iconv \
    php7-json \
    php7-mbstring \
    php7-mcrypt \    
    php7-oauth \
    php7-opcache \
    php7-openssl \
    php7-pcntl \
    php7-pdo \
    php7-pdo_mysql \
    php7-phar \
    php7-redis \
    php7-session \
    php7-simplexml \
    php7-tokenizer \
    php7-xdebug \
    php7-xml \
    php7-xmlwriter \
    php7-zip \
    php7-zlib \
    php7-zmq

RUN chown -R www-data:www-data /var/tmp/nginx/client_body \
    chown -R www-data:www-data /var/tmp/nginx/fastcgi \
    chown -R www-data:www-data /var/tmp/nginx/proxy \
    chown -R www-data:www-data /var/tmp/nginx/scgi \
    chown -R www-data:www-data /var/tmp/nginx/uwsgi


# Composer
RUN curl -sS https://getcomposer.org/installer | php && mv composer.phar /usr/local/bin/composer

# prestissimo - composer parallel install plugin
RUN composer global require "hirak/prestissimo:^0.3"

# Delete cache..
RUN rm -rf /var/cache/apk
RUN rm -rf /root/.composer/cache

# Copy Nginx Files..
COPY config/nginx.conf /etc/nginx/nginx.conf
COPY config/default-nginx /etc/nginx/conf.d/default.conf

# Copy Php Files..
COPY config/www.conf /etc/php7/php-fpm.d/www.conf
COPY config/docker_custom.conf /etc/php7/php-fpm.d/docker_custom.conf
COPY config/php-fpm.conf /etc/php7/php-fpm.conf
COPY config/php.ini /etc/php7/conf.d/docker_custom.ini

# Copy Supervisor and entrypoint files..
COPY config/supervisord.conf /supervisord.conf
COPY config/docker-entrypoint.sh /docker-entrypoint.sh

EXPOSE 8080

WORKDIR /var/www/application

ENTRYPOINT ["tini", "--", "/docker-entrypoint.sh"]
