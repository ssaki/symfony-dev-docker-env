# Simple Docker images for developing and testing with PHP and Symfony

## Development image

Based on official PHP + apache image (php:\d-apache-buster).

Either define a new "docker" environment in your Symfony app or override `APP_ENV`  to `dev`

Default config
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
