FROM php:7.3-fpm

ARG USER
ARG UID

RUN apt-get -y update \
    && apt-get -y install \
        libmcrypt-dev \
        libxslt-dev \
        zlib1g-dev \
        libpng-dev \
        libjpeg-dev \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libzip-dev \
        bash-completion \
        git \
    && apt-get autoremove -y \
    && apt-get autoclean -y \
    && rm -rf /var/lib/apt/lists/*

RUN docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install -j$(nproc) iconv pdo_mysql xsl intl zip bcmath gd soap gettext opcache exif sockets

RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
&& php -r "if (hash_file('sha384', 'composer-setup.php') === '906a84df04cea2aa72f40b5f787e49f22d4c2f19492ac310e8cba5b96ac8b64115ac402c8cd292b8a03482574915d1a8') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;" \
&& php composer-setup.php \
&& php composer-setup.php --version=1.10.20 \
&& php -r "unlink('composer-setup.php');" \
&& mv composer.phar /usr/local/bin/composer

# install xdebug
ARG XDEBUG_VER=stable

RUN pecl install xdebug-${XDEBUG_VER} && docker-php-ext-enable xdebug
COPY ./config/xdebug-config-${XDEBUG_VER}.ini /usr/local/etc/php/conf.d/xdebug-config.ini

# Set memory_limit
RUN echo "php_admin_value[memory_limit] = -1" >> /usr/local/etc/php-fpm.d/www.conf \
&& echo "memory_limit = -1" >> /usr/local/etc/php/conf.d/memory_limit.ini

# Set execution timeout
RUN echo "request_terminate_timeout = 0" >> /usr/local/etc/php-fpm.d/www.conf \
&& echo "max_execution_time = 0" >> /usr/local/etc/php/conf.d/max_execution_time.ini

# Create non-root user
RUN useradd -m -U ${USER} -u ${UID} -p1 -s /bin/bash -G root -o \
    ## Change www-data user to ${USER}
    && sed -i -e "s/www-data/${USER}/" /usr/local/etc/php-fpm.d/www.conf

# Install custom scripts
RUN mkdir -p /usr/local/bin \
&& chmod -R 666 /usr/local/etc/php/conf.d/*

COPY ./bin/* /usr/local/bin/
RUN chmod +x /usr/local/bin/toggle-php-mod

#Install cron
RUN apt-get -y update \
&& apt-get -y install cron

USER ${USER}
