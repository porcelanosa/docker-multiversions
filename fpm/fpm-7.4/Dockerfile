FROM php:7.4-fpm

RUN pear config-set php_ini "$PHP_INI_DIR"
# ставим необходимые для нормальной работы модули
RUN apt-get update && apt-get install -y \
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
    libmcrypt-dev \
    libpq-dev  \
    libicu-dev \
    && docker-php-ext-install -j$(nproc) iconv mbstring mysqli pdo_mysql zip\
    && docker-php-ext-install pdo pdo_pgsql pgsql \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) gd \
    && docker-php-ext-configure intl \
    && docker-php-ext-install intl \
    && docker-php-ext-configure pcntl --enable-pcntl \
    && docker-php-ext-install pcntl

# Clear cache
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

# RUN PATH="$PATH:/usr/bin/zsh"

RUN pecl install xdebug-3.1.5 \
    && docker-php-ext-enable xdebug \
    && pecl install mcrypt-1.0.7 \
    && docker-php-ext-enable mcrypt \
    && echo "xdebug.mode = debug" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.client_host = host.docker.internal" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.start_with_request = yes" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    # && echo "xdebug.client_host = docker.for.mac.localhost" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.client_port = 9074" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.log = /var/log/xdebug.log" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.idekey = PHPSTORM" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini

# RUN pecl channel-update pecl.php.net
# pecl install xdebug && \
# docker-php-ext-enable xdebug \
# Устанавливаем Composer
# RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
COPY --from=composer/composer:latest-bin /composer /usr/bin/composer

# RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
#     php -r "if (hash_file('sha384', 'composer-setup.php') === 'e21205b207c3ff031906575712edab6f13eb0b361f2085f1f1237b7126d785e826a450292b6cfd1d64d92e6563bbde02') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;" \
#     php composer-setup.php \
#     php -r "unlink('composer-setup.php');" \
#     mv composer.phar /usr/local/bin/composer

# Create system user to run Composer and Artisan Commands
# RUN groupadd --gid $gid $uname --non-unique \
#     && useradd --uid $uid --gid $gid $uname -G www-data,root -s /bin/bash

# RUN mkdir -p /home/$uname/.composer && \
#     chown -R $uname:$uname /home/$uname

# Install Composer
# RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Настройка прав доступа
# RUN sudo chown -R www-data:www-data /data/9sky.local/storage \
#     sudo chown -R www-data:www-data /data/9sky.local/bootstrap/cache

WORKDIR /data

VOLUME /data

EXPOSE 9000
EXPOSE 9074

CMD ["php-fpm"]
