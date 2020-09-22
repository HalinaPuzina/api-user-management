FROM php:7.3-apache

LABEL maintainer="Halina Puzina"

ENV APP_PATH="/srv/app/"
ENV DOCKER_PATH="./docker"
ENV APACHE_DOCUMENT_ROOT ${APP_PATH}

COPY ${DOCKER_PATH}/php/php.ini /usr/local/etc/php/

# Copy composer installing script
COPY ${DOCKER_PATH}/composer-installer.sh /usr/local/bin/composer-installer

RUN apt-get -yqq update \
    # Install some soft
    && apt-get -yqq install --no-install-recommends \
        apt-utils \
        default-mysql-client \
        unzip \
        openssh-client \
        libmagickwand-dev \
        libzip-dev \
        libfreetype6-dev \
        libwebp-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
        ffmpeg \
    # Enable some apache modelues
    && a2enmod rewrite negotiation headers \
    # Install Composer
    && chmod +x /usr/local/bin/composer-installer \
    && composer-installer \
    && mv composer.phar /usr/local/bin/composer \
    # Make composer file exutable
    && chmod +x /usr/local/bin/composer \
    # Show composer version
    && composer --version \
    && ffmpeg -version \
    && which ffmpeg \
    && which ffprobe

RUN pecl install xdebug \
    && docker-php-ext-enable xdebug \
    && pecl install imagick-3.4.3 \
    && docker-php-ext-enable imagick \
    && docker-php-ext-configure gd \
        --with-freetype-dir=/usr/include/ \
        --with-webp-dir=/usr/include/ \
        --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install pdo_mysql gd zip bcmath

COPY ${DOCKER_PATH}/php/xdebug-dev.ini /usr/local/etc/php/conf.d/xdebug-dev.ini

RUN cp -R /usr/local/etc/php/conf.d \
    /usr/local/etc/php/conf.d-dev \
    && rm -f /usr/local/etc/php/conf.d/*-dev.ini \
    && rm -f /usr/local/etc/php/conf.d/*xdebug.ini

RUN sed -ri -e 's!/var/www/html!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/sites-available/*.conf
RUN sed -ri -e 's!/var/www/!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/apache2.conf /etc/apache2/conf-available/*.conf

COPY ${DOCKER_PATH}/apache/vhost.conf /etc/apache2/sites-available/000-default.conf

WORKDIR ${APP_PATH}
