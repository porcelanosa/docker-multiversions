FROM php:8.3-fpm

# Установка системных зависимостей
RUN pear config-set php_ini "$PHP_INI_DIR" && \
    apt-get update && apt-get install -y \
    supervisor \
    curl \
    sudo \
    nano \
    openssh-server \
    wget \
    git \
    zsh \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    libpng-dev \
    libonig-dev \
    libzip-dev \
    libpq-dev \
    libicu-dev \
    && docker-php-ext-install -j$(nproc) iconv mbstring mysqli pdo_mysql zip sockets \
    && docker-php-ext-install pdo pdo_pgsql pgsql \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) gd \
    && docker-php-ext-configure intl \
    && docker-php-ext-install intl pcntl exif \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# Установка Xdebug
RUN pecl install xdebug && \
    docker-php-ext-enable xdebug && \
    echo "xdebug.mode = debug" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo "xdebug.client_host = host.docker.internal" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo "xdebug.start_with_request = yes" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo "xdebug.client_port = 9003" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo "xdebug.idekey = PHPSTORM" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini

# Настройка окружения
RUN mkdir -p /var/log/supervisor && \
    mkdir -p /var/run/supervisor && \
    chown -R www-data:www-data /var/log/supervisor /var/run/supervisor && \
    chmod 770 /var/run/supervisor && \
    touch /var/log/xdebug.log && \
    chown www-data:www-data /var/log/xdebug.log && \
    chmod 666 /var/log/xdebug.log

# Установка Zsh и алиасов
RUN sh -c "$(wget -O- https://github.com/deluan/zsh-in-docker/releases/download/v1.2.1/zsh-in-docker.sh)" && \
    echo "alias ll='ls -lah'" >> /root/.zshrc && \
    echo "alias gs='git status'" >> /root/.zshrc && \
    echo "alias art='php artisan'" >> /root/.zshrc && \
    chsh -s $(which zsh) root

# Установка Node.js
ARG NODE_VERSION=22
RUN curl -sLS https://deb.nodesource.com/setup_$NODE_VERSION.x | bash - && \
    apt-get install -y nodejs && \
    npm install -g npm@latest

# Установка Composer
COPY --from=composer/composer:latest-bin /composer /usr/bin/composer

# Копирование конфигураций
COPY ./supervisor/conf.d/ /etc/supervisor/conf.d/
COPY start-container /usr/local/bin/start-container
RUN chmod +x /usr/local/bin/start-container

WORKDIR /data
VOLUME /data
EXPOSE 9000 9003 9383 8080

CMD ["/usr/local/bin/start-container"]