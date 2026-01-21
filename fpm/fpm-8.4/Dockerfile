# Используем официальный образ PHP 8.4.11-fpm
FROM php:8.4.11-fpm

# Устанавливаем переменные окружения
ARG NODE_VERSION=22
ENV GENERATE_SOURCEMAP=false
ENV NODE_OPTIONS=--max_old_space_size=16384

# Устанавливаем зависимости и необходимые пакеты
RUN apt-get update && apt-get install -y \
    curl \
    sudo \
    nano \
    wget \
    git \
    zsh \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    libpng-dev \
    libonig-dev \
    libzip-dev \
    libmcrypt-dev \
    libpq-dev \
    libmagickwand-dev \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# Устанавливаем PHP-расширения
RUN docker-php-ext-configure gd --with-freetype --with-jpeg --with-webp \
    && docker-php-ext-install -j$(nproc) iconv mbstring mysqli pdo_mysql zip exif pdo pdo_pgsql pgsql gd sockets

# Устанавливаем PECL-расширения (imagick, redis, xdebug)
RUN pecl channel-update pecl.php.net \
    && pecl install imagick redis xdebug \
    && docker-php-ext-enable imagick redis xdebug

# Настраиваем Xdebug
RUN echo "xdebug.mode=debug" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.client_host=host.docker.internal" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.start_with_request=yes" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.client_port=9084" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.log=/var/log/xdebug.log" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.idekey=PHPSTORM" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini

# Устанавливаем Composer
COPY --from=composer/composer:latest-bin /composer /usr/bin/composer

# Устанавливаем Node.js и npm
RUN curl -sLS https://deb.nodesource.com/setup_${NODE_VERSION}.x | bash - \
    && apt-get install -y nodejs \
    && npm install -g npm@11.2.0 \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# Устанавливаем Zsh и powerline10k
RUN sh -c "$(wget -O- https://github.com/deluan/zsh-in-docker/releases/download/v1.2.1/zsh-in-docker.sh)"

# Создаем .zshrc с алиасами (исправлена функция mm)
RUN echo "alias la='ls -lah'" >> /root/.zshrc \
    && echo "alias ll='ls -lh'" >> /root/.zshrc \
    && echo "alias gs='git status'" >> /root/.zshrc \
    && echo "alias art='sudo -u www-data php artisan'" >> /root/.zshrc \
    && echo "alias mig='php artisan migrate'" >> /root/.zshrc \
    && echo 'mm() { php artisan make:migration "$1"; }' >> /root/.zshrc \
    && echo "alias _='sudo'" >> /root/.zshrc \
    && echo "alias ccl='art cache:clear'" >> /root/.zshrc \
    && echo "alias arto='art optimize'" >> /root/.zshrc \
    && echo "alias clr='art route:clear'" >> /root/.zshrc

# Устанавливаем Zsh как shell по умолчанию (объединяем команды)
RUN chsh -s $(which zsh) root && \
    # Копируем .zshrc в /etc/zsh/zshrc для глобального применения
    cp /root/.zshrc /etc/zsh/zshrc || true

# Настраиваем рабочую директорию
WORKDIR /data
VOLUME /data

# Открываем порты
EXPOSE 9000 9084

# Запускаем php-fpm с Zsh (опционально)
CMD ["php-fpm"]