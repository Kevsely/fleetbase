FROM php:8.2-fpm-bullseye

# Install system dependencies
RUN apt-get update && apt-get install -y \
    git unzip libzip-dev libpng-dev libonig-dev libxml2-dev \
    libgeos-dev libgeos++-dev autoconf build-essential pkg-config \
    nodejs npm \
    && docker-php-ext-install pdo_mysql bcmath gd zip intl sockets pcntl \
    && pecl install redis apcu \
    && docker-php-ext-enable redis apcu

# Install composer
COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

# Set working dir
WORKDIR /var/www/html

# Copy composer files first (for better caching)
COPY ./api/composer.json ./api/composer.lock ./

# Install PHP dependencies
RUN composer install --no-dev --optimize-autoloader --no-scripts

# Copy application code
COPY ./api .

RUN php artisan package:discover --ansi || true

# Set permissions for storage and bootstrap cache
RUN mkdir -p storage bootstrap/cache && \
    chown -R www-data:www-data storage bootstrap/cache && \
    chmod -R 775 storage bootstrap/cache

# Expose port
EXPOSE 8000

# Start Laravel
CMD ["php", "artisan", "serve", "--host=0.0.0.0", "--port=8000"]
