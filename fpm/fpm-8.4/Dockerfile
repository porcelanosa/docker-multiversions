FROM php:8.4.5-fpm
RUN pear config-set php_ini "$PHP_INI_DIR"
# ставим необходимые для нормальной работы модули
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
    libpq-dev  \
    # Зависимости для ImageMagick
    libmagickwand-dev

# Clear cache
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

RUN docker-php-ext-install -j$(nproc) iconv mbstring mysqli pdo_mysql zip\
    && docker-php-ext-install pdo pdo_pgsql pgsql \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) gd

RUN pecl channel-update pecl.php.net
RUN pecl install imagick redis  && docker-php-ext-enable imagick redis

# Uncomment to enable Xdebug then run docker-compose up -d --build
# RUN pecl install xdebug-3.1.5
# COPY docker/php/90-xdebug.ini "${PHP_INI_DIR}/conf.d"
# RUN PATH="$PATH:/usr/bin/zsh"
RUN pecl install xdebug \
    && docker-php-ext-enable xdebug
    # \
    # && pecl install mcrypt-1.0.7 \
    # && docker-php-ext-enable mcrypt
RUN echo "xdebug.mode = debug" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.client_host = host.docker.internal" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.start_with_request = yes" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    # && echo "xdebug.client_host = docker.for.mac.localhost" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.client_port = 9003" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.log = /var/log/xdebug.log" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.idekey = PHPSTORM" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini


# Default powerline10k theme, no plugins installed
RUN sh -c "$(wget -O- https://github.com/deluan/zsh-in-docker/releases/download/v1.2.1/zsh-in-docker.sh)"

# Создаем .zshrc с алиасами
RUN echo "alias ll='ls -lah'" >> /root/.zshrc && \
    echo "alias gs='git status'" >> /root/.zshrc && \
    echo "alias art='php artisan'" >> /root/.zshrc

# Устанавливаем Zsh как shell по умолчанию
RUN chsh -s $(which zsh) root
RUN zsh

# RUN pecl channel-update pecl.php.net
# pecl install xdebug && \
# docker-php-ext-enable xdebug \
# Устанавливаем Composer
# RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
COPY --from=composer/composer:latest-bin /composer /usr/bin/composer

ARG NODE_VERSION=22
ENV GENERATE_SOURCEMAP=false
ENV NODE_OPTIONS=--max_old_space_size=16384
# Install NODE and NPM
RUN curl -sLS https://deb.nodesource.com/setup_$NODE_VERSION.x | bash - \
    && apt-get install -y nodejs \
    && npm install -g npm@11.2.0

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

RUN php -i | grep xdebug

WORKDIR /data

VOLUME /data

EXPOSE 9000
EXPOSE 9003

CMD ["php-fpm"]
