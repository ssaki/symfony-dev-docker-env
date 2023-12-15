FROM php:8.3-apache

MAINTAINER Alexander Marinov <alex.g.marinov@gmail.com>

ENV PROJECT_ROOT=/var/www/html/
ENV APACHE_DOCUMENT_ROOT /var/www/html/public
ENV APACHE_SERVER_NAME "localhost"
ENV XDEBUG_REMOTE_HOST "host.docker.internal"
# Symfony environmental variables
ENV APP_ENV "dev"
ENV APP_DEBUG "true"

# ====================================================================
# Install system dependencies
RUN apt-get update -q -y \
    ### System deps
    && apt-get install -q -y --no-install-recommends \
            # install inzip and git - both needed by composer
            unzip \
            git \
            # deps of ext-zip
            zlib1g-dev libzip-dev \
            # deps for imagic
            libgraphicsmagick1-dev libpng-dev libjpeg-dev \
            # deps for AMQP
            librabbitmq-dev libssh-dev \
            # and few helper tools for CLI
            vim wget iputils-ping \
    # Clean up
    && apt-get autoremove -y \
    && apt-get clean all \
    && rm -rf /var/lib/apt/lists/*

# ====================================================================
# Setup PHP
RUN docker-php-source extract \
    # Install XDebug for debug && code coverage
    && pecl install xdebug \
    && docker-php-ext-enable xdebug \
    # image processing extensions
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install gd \
    # Install/Enable common extensions
    && docker-php-ext-install -j$(nproc) bcmath \
    && docker-php-ext-install -j$(nproc) sockets \
    && docker-php-ext-install -j$(nproc) pdo_mysql \
    && docker-php-ext-install -j$(nproc) zip \
    # Install/Enable Redis PHP extension
    && pecl install redis \
    && docker-php-ext-enable redis \
    # Install/Enable AMQP
    && pecl install amqp \
    && docker-php-ext-enable amqp \
    # Clean up
    && docker-php-source delete \
    && pecl clear-cache \
    && rm -Rf "$(pecl config-get temp_dir)/*"

# Install composer
RUN curl -sS https://getcomposer.org/installer | php \
    && mv composer.phar /usr/local/bin/composer \
    && chmod +x /usr/local/bin/composer

WORKDIR $PROJECT_ROOT

RUN a2enmod rewrite

RUN echo '\
<VirtualHost *:80> \n\
    ServerName ${APACHE_SERVER_NAME} \n\
    ServerAdmin webmaster@localhost \n\
    DocumentRoot ${APACHE_DOCUMENT_ROOT} \n\
    ErrorLog ${APACHE_LOG_DIR}/error.log \n\
    CustomLog ${APACHE_LOG_DIR}/access.log combined \n\
    <Directory ${APACHE_DOCUMENT_ROOT}> \n\
        AllowOverride None \n\
        Order Allow,Deny \n\
        Allow from All \n\
        FallbackResource /index.php \n\
    </Directory> \n\
    SetEnvIf Authorization "(.*)" HTTP_AUTHORIZATION=$1 \n\
</VirtualHost> \n\
' > /etc/apache2/sites-available/000-default.conf

RUN sed -ri -e 's!/var/www/!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/apache2.conf /etc/apache2/conf-available/*.conf

RUN echo '\
xdebug.mode=debug \n\
xdebug.start_with_request=yes \n\
xdebug.client_host=${XDEBUG_REMOTE_HOST} \n\
xdebug.client_port=9003 \n\
xdebug.log_level=0 \n\
' > /usr/local/etc/php/conf.d/90-xdebug-custom.ini

EXPOSE 80
