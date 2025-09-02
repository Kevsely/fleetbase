# Base stage
FROM dunglas/frankenphp:1.5.0-php8.2-bookworm AS base

# Install packages
RUN apt-get update && apt-get install -y git nodejs npm uuid-runtime build-essential pkg-config libssl-dev libzstd-dev libbrotli-dev libcurl4-openssl-dev \
    && mkdir -p /root/.ssh && ssh-keyscan github.com >> /root/.ssh/known_hosts

# PHP extensions (alléger)
RUN install-php-extensions pdo_mysql gd bcmath redis intl zip gmp apcu opcache sockets pcntl @composer \
    && pecl install swoole \
    && docker-php-ext-enable swoole

# PHP config
RUN sed -i -e 's/^memory_limit.*/memory_limit = 512M/' "$PHP_INI_DIR/php.ini"

# Composer cache
RUN mkdir -p /var/www/.cache/composer && chown -R www-data:www-data /var/www/.cache/composer

# Workdir
WORKDIR /fleetbase/api

# Copy app
COPY --chown=www-data:www-data ./api /fleetbase/api

# Composer install
RUN su www-data -s /bin/sh -c "composer install --no-scripts --optimize-autoloader --no-dev --no-cache"

# Octane Swoole start (1 worker, moins de RAM)
CMD ["php", "artisan", "octane:start", "--server=swoole", "--host=0.0.0.0", "--port=8000", "--workers=1"]
