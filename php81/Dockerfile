FROM php:8.1-fpm

ARG USER_UID
ARG USER_GID

RUN groupadd -g ${USER_GID} yurirene && \
    useradd -u ${USER_UID} -g yurirene -m yurirene && \
    usermod -p "*" yurirene

# Dependências de build/runtime mínimas para extensões usadas pelo Laravel + Composer
RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    libcurl4-openssl-dev \
    libicu-dev \
    libonig-dev \
    libpng-dev \
    libjpeg-dev \
    libzip-dev \
    zlib1g-dev \
    nano \
    && rm -rf /var/lib/apt/lists/*

RUN docker-php-ext-configure gd --with-jpeg \
    && docker-php-ext-install -j$(nproc) \
    bcmath \
    curl \
    exif \
    gd \
    intl \
    mbstring \
    pcntl \
    pdo_mysql \
    zip

RUN curl -fsSL https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

RUN rm -rf /var/www/html

WORKDIR /var/www

COPY .bash_aliases /home/yurirene/.bash_aliases
COPY zz-fpm-user.conf /usr/local/etc/php-fpm.d/zz-fpm-user.conf

RUN chown yurirene:yurirene /home/yurirene/.bash_aliases
