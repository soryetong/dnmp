FROM php:7.4-fpm

ENV TZ=Asia/Shanghai

ADD sources_debian11.list /etc/apt/sources.list

COPY --from=mlocati/php-extension-installer /usr/bin/install-php-extensions /usr/bin/

RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime \
    && echo $TZ > /etc/timezone \ 
    && DEBIAN_FRONTEND=noninteractive apt-get update -q \
    && DEBIAN_FRONTEND=noninteractive apt-get install -qq -y \
        curl \
        git  \
        zip unzip \
    && install-php-extensions \
        bcmath \
        bz2 \
        calendar \
        exif \
        gd \
        intl \
        ldap \
        mysqli \
        opcache \
        pdo_mysql \
        pdo_pgsql \
        pgsql \
        redis \
        soap \
        xsl \
        zip \
        sockets \
        yaf \
        memcached \
        mongodb-stable \
        iconv \
        mbstring \
        gettext \
        gmp \
        ftp \
        pcntl \
        shmop \
        sysvsem \
        sysvshm \
        odbc \
        xmlrpc \
        geoip \
        imagick \
    && docker-php-ext-enable \
        bcmath \
        exif \
        gmp \
        mysqli \
        pcntl \
        shmop \
        sysvsem \
        xsl \
        bz2 \
        gd \
        intl \
        memcached \
        odbc \
        pdo_mysql \
        pgsql \
        soap \
        sysvshm \
        yaf \
        calendar \
        gettext \
        ldap \
        mongodb \
        pdo_pgsql \
        redis \
        sockets \
        xmlrpc \
        zip \
        geoip \
        imagick

# 创建存放扩展的目录
ENV EXTPATH=/usr/src/php/ext
RUN mkdir -p $EXTPATH


# # Xdebug 
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

# 设置工作目录
WORKDIR /var/www/html