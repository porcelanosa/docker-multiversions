# Используем официальный образ PHP 8.4 FPM
FROM php:8.4-fpm

# Аргументы и переменные окружения
ARG NODE_VERSION=22

ENV GENERATE_SOURCEMAP=false
ENV NODE_OPTIONS=--max_old_space_size=16384

# Устанавливаем системные зависимости
RUN apt-get update && apt-get install -y \
    supervisor \
    curl \
    sudo \
    nano \
    psmisc \
    openssh-server \
    wget \
    git \
    zsh \
    unzip \
    zip \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    libpng-dev \
    libonig-dev \
    libzip-dev \
    libpq-dev \
    libxml2-dev \
    libicu-dev \
    libcurl4-openssl-dev \
    libmagickwand-dev \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Настройка GD
RUN docker-php-ext-configure gd \
    --with-freetype \
    --with-jpeg \
    --with-webp

# Установка PHP расширений
RUN docker-php-ext-install -j$(nproc) \
    bcmath \
    exif \
    gd \
    intl \
    mbstring \
    mysqli \
    pdo_mysql \
    pdo_pgsql \
    pgsql \
    sockets \
    xml \
    zip

# Установка PECL расширений
RUN pecl channel-update pecl.php.net \
    && pecl install imagick redis xdebug \
    && docker-php-ext-enable imagick redis xdebug

# Настройка Xdebug
RUN echo "xdebug.mode=debug" > /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.client_host=host.docker.internal" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.start_with_request=yes" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.client_port=9084" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.log=/var/log/xdebug.log" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.idekey=PHPSTORM" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini

# Composer
COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

# Node.js
RUN curl -fsSL https://deb.nodesource.com/setup_${NODE_VERSION}.x | bash - \
    && apt-get install -y nodejs \
    && npm install -g npm@latest \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Zsh + Powerlevel10k
RUN sh -c "$(wget -O- https://github.com/deluan/zsh-in-docker/releases/download/v1.2.1/zsh-in-docker.sh)"

# Полезные алиасы
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

# Zsh по умолчанию
RUN chsh -s $(which zsh) root \
    && cp /root/.zshrc /etc/zsh/zshrc || true

# Рабочая директория
WORKDIR /data

# Том для проектов
VOLUME ["/data"]

# PHP-FPM
EXPOSE 9000

CMD ["php-fpm"]