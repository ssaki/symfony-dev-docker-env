# Simple Docker images for developing and testing with PHP and Symfony

## Development image

Based on official PHP + apache image (php:8.*-apache)

**_This is a rolling release!!_**

It will always be based on the latest minor PHP release at the time of building the image!

## 
This setup is intended for quickly bootstrapping a typical Symfony app that has a RDBMS with the "have a reproducible local environment" mindset 
rather than attempting to mimic arbitrary "production-like" (whatever that could mean) setup. if your mindset differs then you might want to look elsewhere.


# Usage 

Either define a new "docker" environment in your Symfony app or override `APP_ENV`  to `dev`

# Compose example

```yaml
volumes:
    database:

services:
    web:
        image: weblaboratoryltd/symfony-dev:8
        volumes:
          - .:/var/www/html
        links:
            - mysql
        ports:
            - "80:80"
        extra_hosts:
            - "host.docker.internal:host-gateway"
        environment:
            PW_DEBUG: 'true'
            APP_ENV: dev

    mysql:
        image: mysql:8-debian
        command: --default-authentication-plugin=mysql_native_password
        restart: always
        ports:
            - "3306:3306"
        volumes:
            - database:/var/lib/mysql
        environment:
            MYSQL_ROOT_PASSWORD: q1w2e3
            MYSQL_DATABASE: symfony
            MYSQL_USER: symfony
            MYSQL_PASSWORD: q1w2e3


```

# Default config

```dockerfile
ENV PROJECT_ROOT=/var/www/html/
ENV APACHE_DOCUMENT_ROOT /var/www/html/public
ENV XDEBUG_REMOTE_HOST "host.docker.internal"
ENV APP_ENV "docker"
```

For debugging on Apple configure the host in your compose file: 
```dockerfile
    environment:
        - XDEBUG_REMOTE_HOST=docker.for.mac.localhost
```

For debugging on linux pass an extra host config:
```dockerfile
    extra_hosts:
        - "host.docker.internal:host-gateway"
```
