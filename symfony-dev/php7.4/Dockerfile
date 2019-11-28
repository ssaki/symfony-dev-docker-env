FROM php:7.4-apache-buster

MAINTAINER Alexander Marinov <alexander.marinov@web-lab.ltd>

RUN apt-get update -q -y \
    ### System deps
    && apt-get install -q -y --no-install-recommends \
            # install inzip and git - both needed by composer
            unzip \
            git \
            # deps of ext-zip
            zlib1g-dev \
            libzip-dev \
    ### PHP
    # Install XDebug for debug && code coverage
    && docker-php-source extract \
    && pecl install xdebug \
    && docker-php-ext-enable xdebug \
    # Install/Enable common extensions
    && docker-php-ext-install -j$(nproc) bcmath \
    && docker-php-ext-install -j$(nproc) pdo_mysql \
    && docker-php-ext-install -j$(nproc) zip \
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
RUN a2enmod rewrite

RUN echo '\
<VirtualHost *:80> \n\
	#ServerName www.example.com \n\
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

EXPOSE 80
