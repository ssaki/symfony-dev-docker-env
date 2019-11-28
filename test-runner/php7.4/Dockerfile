FROM php:7.4-cli-buster

MAINTAINER Alexander Marinov <alexander.marinov@web-lab.ltd>

RUN apt-get update -q -y \
    ### System deps
    && apt-get install -q -y --no-install-recommends \
            # install inzip and git - both needed by composer
            unzip \
            git \
    ### PHP
    && docker-php-source extract \
    && pecl install xdebug \
    && docker-php-ext-enable xdebug \
    # Install/Enable common extensions
    && docker-php-ext-install -j$(nproc) bcmath \
    && docker-php-ext-install -j$(nproc) pdo_mysql \
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

# Install PHPUnit 8
RUN curl -O -L -sS  https://phar.phpunit.de/phpunit-8.phar \
    && mv phpunit-8.phar /usr/local/bin/phpunit \
    && chmod +x /usr/local/bin/phpunit

# Also have PHPUnit 7 for older projects
RUN curl -O -L -sS  https://phar.phpunit.de/phpunit-7.phar \
    && mv phpunit-7.phar /usr/local/bin/phpunit7 \
    && chmod +x /usr/local/bin/phpunit7