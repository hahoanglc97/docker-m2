FROM php:7.4-fpm-alpine

ARG USER
ARG UID

# Create non-root user
RUN useradd -m -U ${USER} -u ${UID} -p1 -s /bin/bash -G root -o \
    ## Change www-data user to ${USER}
    && sed -i -e "s/www-data/${USER}/" /usr/local/etc/php-fpm.d/www.conf

# Install custom scripts
RUN mkdir -p /usr/local/bin \
&& chmod -R 666 /usr/local/etc/php/conf.d/*

COPY ./bin/* /usr/local/bin/
RUN chmod +x /usr/local/bin/toggle-php-mod

USER ${USER}
