FROM php:7.1-fpm-alpine

# environments
ENV TIMEZONE Europe/Kiev

# packages
RUN apk add --update --no-cache \
    icu-dev \
    libxslt-dev \
    libpng-dev \
    libmcrypt-dev \
    libjpeg-turbo \
    libjpeg-turbo-dev \
    freetype \
    freetype-dev \
    imagemagick-dev \
    imagemagick \
    libtool \
    git \
    mc \
    curl \
    unzip \
    mysql-client \
    ca-certificates openssl openssh \
    pcre-dev \
    autoconf \
	file \
	g++ \
	gcc \
	libc-dev \
	make \
	pkgconf \
	re2c

# install gd
RUN docker-php-ext-configure gd \
    --with-gd \
    --with-freetype-dir=/usr/include/ \
    --with-png-dir=/usr/include/ \
    --with-jpeg-dir=/usr/include/ && \
  NPROC=$(grep -c ^processor /proc/cpuinfo 2>/dev/null || 1) && \
  docker-php-ext-install -j${NPROC} gd && \
  apk del --no-cache freetype-dev libpng-dev libjpeg-turbo-dev

RUN docker-php-source extract \
        && pecl install xdebug redis imagick \
        && docker-php-ext-enable xdebug redis imagick \
        && docker-php-source delete \
        && docker-php-ext-install mbstring intl xsl exif pdo_mysql soap simplexml opcache mcrypt \
        && rm -rf /tmp/*

RUN set -x \
    && addgroup -g 1000 -S dev \
    && adduser -u 1000 -D -S -G dev dev \
    && addgroup www-data dev

# composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/bin --filename=composer

# set time zone
RUN echo "Set default timezone - $TIMEZONE"
RUN echo "$TIMEZONE" > /etc/timezone

COPY entrypoint.sh /usr/bin/entrypoint.sh
COPY ./rootfs/php.ini /usr/local/etc/php/php.ini
COPY ./rootfs/docker-php-ext-xdebug.ini /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini

RUN chmod u+x /usr/bin/entrypoint.sh

ENTRYPOINT ["/usr/bin/entrypoint.sh"]

EXPOSE 9000

CMD ["php-fpm"]