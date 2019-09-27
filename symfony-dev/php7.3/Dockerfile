FROM php:7.3-cli-buster

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

# Install the Symfony command
RUN curl -O -L -sS  https://github.com/symfony/cli/releases/download/v4.7.0/symfony_linux_amd64.gz \
    && gzip -d symfony_linux_amd64.gz \
    && mv symfony_linux_amd64 /usr/local/bin/symfony \
    && chmod +x /usr/local/bin/symfony

ENV PROJECT_ROOT=/usr/src/symfony-app
WORKDIR $PROJECT_ROOT

EXPOSE 8000

CMD ["sh", "-c",  "symfony serve --no-tls --dir=$PROJECT_ROOT"]
