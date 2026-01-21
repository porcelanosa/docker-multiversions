FROM php:5.6-fpm

# === Настройка устаревших репозиториев Debian Jessie ===
RUN echo "deb [check-valid-until=no] http://archive.debian.org/debian jessie main" > /etc/apt/sources.list.d/jessie.list && \
    echo "deb [check-valid-until=no] http://archive.debian.org/debian-security jessie/updates main" >> /etc/apt/sources.list.d/jessie.list && \
    echo 'Acquire::Check-Valid-Until "false";' > /etc/apt/apt.conf.d/90ignore-release-date

# === Обновление и установка базовых утилит ===
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        git curl wget zip unzip nano sudo && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# === Установка зависимостей для GD (без JPEG) ===
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        zlib1g-dev \
        libfreetype6-dev \
        libpng-dev && \
    docker-php-ext-configure gd \
        --with-freetype-dir=/usr \
        --with-png-dir=/usr && \
    docker-php-ext-install gd && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# === Установка остальных PHP-расширений ===
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        libmcrypt-dev \
        libpq-dev \
        libicu-dev \
        libxml2-dev && \
    docker-php-ext-install mbstring mcrypt pdo_mysql intl zip && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# === Установка Composer (версия 1.x, совместимая с PHP 5.6) ===
RUN curl -sS https://getcomposer.org/installer | php -- --version=1.10.26 --install-dir=/usr/local/bin --filename=composer

# === Настройка рабочей директории ===
WORKDIR /var/www/html

# === Порты ===
EXPOSE 9000

# === Запуск PHP-FPM ===
CMD ["php-fpm"]