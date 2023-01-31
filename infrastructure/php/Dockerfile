FROM node:latest AS node
FROM php:8.2-fpm

ARG TIMEZONE

COPY --from=node /usr/local/lib/node_modules /usr/local/lib/node_modules
COPY --from=node /usr/local/bin/node /usr/local/bin/node
RUN ln -s /usr/local/lib/node_modules/npm/bin/npm-cli.js /usr/local/bin/npm

COPY php.ini /usr/local/etc/php/php.ini
COPY php-fpm.conf /etc/php/php-fpm.d/

RUN apt-get update && apt-get install -y \
    gnupg \
    g++ \
    procps \
    openssl \
    git \
    unzip \
    zlib1g-dev \
    libzip-dev \
    libfreetype6-dev \
    libpng-dev \
    libjpeg-dev \
    libicu-dev  \
    libonig-dev \
    libxslt1-dev \
    acl \
    && echo 'alias sf="php bin/console"' >> ~/.bashrc

RUN docker-php-ext-configure \
    gd --with-jpeg --with-freetype

RUN docker-php-ext-install \
      pdo pdo_mysql zip xsl gd intl opcache exif mbstring

RUN pecl install xdebug apcu \
    && docker-php-ext-enable xdebug apcu

# Set timezone
RUN ln -snf /usr/share/zoneinfo/${TIMEZONE} /etc/localtime && echo ${TIMEZONE} > /etc/timezone \
    && printf '[PHP]\ndate.timezone = "%s"\n', ${TIMEZONE} > /usr/local/etc/php/conf.d/tzone.ini \
    && "date"

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

#RUN npm install -g yarn

WORKDIR /var/www/app

CMD ["php-fpm", "-F"]

EXPOSE 9000
