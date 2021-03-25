ARG PHP_VERSION
FROM ${PHP_VERSION}

RUN echo "deb http://mirrors.aliyun.com/debian/ buster main non-free contrib \n \
deb-src http://mirrors.aliyun.com/debian/ buster main non-free contrib \n \
deb http://mirrors.aliyun.com/debian-security buster/updates main \n \
deb-src http://mirrors.aliyun.com/debian-security buster/updates main \n \
deb http://mirrors.aliyun.com/debian/ buster-updates main non-free contrib \n \
deb-src http://mirrors.aliyun.com/debian/ buster-updates main non-free contrib \n \
deb http://mirrors.aliyun.com/debian/ buster-backports main non-free contrib \n \
deb-src http://mirrors.aliyun.com/debian/ buster-backports main non-free contrib" > /etc/apt/sources.list \
    && apt-get update \
    && apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    libmagickcore-dev \
    libmagickwand-dev \
    imagemagick \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    libmcrypt-dev \
    libpng-dev \
    libzip-dev \
    libwebp-dev \
    libicu-dev \
    pkg-config \
    libcurl4-openssl-dev \
    libssl-dev \
    apt-utils \
    curl     \
    libmemcached-dev \
    && docker-php-ext-configure gd  --with-webp=/usr/include/webp --with-jpeg=/usr/include --with-freetype=/usr/include/freetype2/ \
    && docker-php-ext-install -j$(nproc) gd iconv \
    pdo_mysql mysqli bcmath calendar exif gettext sockets dba \
    pcntl shmop sysvmsg sysvsem sysvshm opcache \
    curl intl \
    && pecl install igbinary \
    && pecl install imagick \
    && pecl install mcrypt \
    && pecl install msgpack \
    && pecl install memcached \
    && pecl install zip \
    && pecl install redis \
    && pecl install mongodb \
    && pecl install swoole \
    && docker-php-ext-enable igbinary  imagick mcrypt \
    msgpack memcached  zip redis mongodb swoole \
    && mv "$PHP_INI_DIR/php.ini-development" "$PHP_INI_DIR/php.ini"


WORKDIR /var/www
