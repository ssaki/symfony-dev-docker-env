FROM php:7.3-apache

MAINTAINER Alexander Marinov <alexander.marinov@web-lab.ltd>

RUN apt-get update -q -y \
    ### System deps
    && apt-get install -q -y --no-install-recommends \
            # install inzip and git - both needed by composer
            unzip \
            git \
            # deps of pdo_pgsql
            libpq-dev \
            # deps of ext-zip
            zlib1g-dev \
            libzip-dev \
            # recode
            librecode-dev \
    ### PHP
    && docker-php-source extract \
    && pecl install xdebug \
    && docker-php-ext-enable xdebug \
    # Install/Enable common extensions
    && docker-php-ext-configure pgsql -with-pgsql=/usr/local/pgsql \
    && docker-php-ext-install -j$(nproc) pdo_pgsql pgsql \
    && docker-php-ext-install -j$(nproc) bcmath \
    && docker-php-ext-install -j$(nproc) pdo_mysql \
    && docker-php-ext-install -j$(nproc) pdo_pgsql \
    && docker-php-ext-install -j$(nproc) recode \
    && docker-php-ext-install -j$(nproc) sockets \
    && docker-php-ext-install -j$(nproc) zip \
    # Install XDebug for code coverage
    # Clean up
    && docker-php-source delete \
    && pecl clear-cache \
    && rm -Rf "$(pecl config-get temp_dir)/*" \
    && apt-get autoremove -y \
    && apt-get clean all \
    && rm -rf /var/lib/apt/lists/*

# Install composer
RUN curl -sS https://getcomposer.org/installer | php \
    && mv composer.phar /usr/local/bin/composer \
    && chmod +x /usr/local/bin/composer

ENV PROJECT_ROOT=/var/www/html/
WORKDIR $PROJECT_ROOT

ENV APACHE_DOCUMENT_ROOT /var/www/html/public

RUN sed -ri -e 's!/var/www/html!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/sites-available/*.conf
RUN sed -ri -e 's!/var/www/!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/apache2.conf /etc/apache2/conf-available/*.conf

EXPOSE 80
