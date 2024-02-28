FROM php:7.1-fpm

ENV TZ=Asia/Shanghai

ADD sources_debian9.list /etc/apt/sources.list

RUN apt-get update \
    && apt-get install -y -f --no-install-recommends --allow-downgrades --fix-missing \
        curl \
        unzip \
        libhiredis-dev \
        gcc \
        make \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        libxslt-dev \
        librabbitmq-dev \
        libxml2=2.9.4+dfsg1-2.2+deb9u7 \
        zlib1g=1:1.2.8.dfsg-5+deb9u1 \
        libxml2-dev \
        zlib1g-dev \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd \
        iconv \
        bcmath \
        pdo_mysql \
        calendar \
        exif \
        gettext \
        pcntl \
        shmop \
        sockets \
        zip \
        opcache \
        mcrypt \
    && docker-php-ext-enable \
        iconv \
        bcmath \
        pdo_mysql \
        calendar \
        exif \
        gettext \
        pcntl \
        shmop \
        sockets \
        zip \
        opcache \
        mcrypt \
    && rm -rf /var/lib/apt/lists/*

# 创建存放扩展的目录
ENV EXTPATH=/usr/src/php/ext
RUN mkdir -p $EXTPATH

# Redis
COPY ./extension/phpredis-5.3.4.tar.gz /tmp/
RUN tar -xzf /tmp/phpredis-5.3.4.tar.gz \
    && rm -rf /tmp/phpredis-5.3.4.tar.gz \
    && mv phpredis-5.3.4 $EXTPATH/redis \
    && docker-php-ext-install redis

# Xdebug 
COPY ./extension/xdebug-2.8.1.tar.gz /tmp/
RUN tar -xzf /tmp/xdebug-2.8.1.tar.gz \
    && rm -rf /tmp/xdebug-2.8.1.tar.gz \
    && mv xdebug-2.8.1 $EXTPATH/xdebug \
    && docker-php-ext-install xdebug

# Swoole 
COPY ./extension/swoole-src-4.4.8.tar.gz /tmp/
RUN tar -xzf /tmp/swoole-src-4.4.8.tar.gz \
    && rm -rf /tmp/swoole-src-4.4.8.tar.gz \
    && mv swoole-src-4.4.8 $EXTPATH/swoole \
    && docker-php-ext-install swoole

# MongoDB
COPY ./extension/mongodb-1.11.1.tgz /tmp/
RUN tar -xzf /tmp/mongodb-1.11.1.tgz \
    && rm -rf /tmp/mongodb-1.11.1.tgz \
    && mv mongodb-1.11.1 $EXTPATH/mongodb \
    && docker-php-ext-install mongodb

# 设置工作目录
WORKDIR /var/www/html