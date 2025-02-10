FROM php:8.2-fpm

WORKDIR /var/www

RUN rm /bin/sh && ln -s /bin/bash /bin/sh

RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y locales locales-all build-essential libgnutls30
ENV LC_ALL ru_RU.UTF-8
ENV LANG ru_RU.UTF-8
ENV LANGUAGE ru_RU.UTF-8

RUN apt-get install -y \
        git \
        ca-certificates \
        zip \
        libzip-dev \
		libfreetype6-dev \
		libjpeg62-turbo-dev \
		libpng-dev \
		libicu-dev \
    && update-ca-certificates \
	&& docker-php-ext-configure gd \
	&& docker-php-ext-configure intl \
	&& docker-php-ext-install -j$(nproc) gd zip intl pdo_mysql

RUN git config --global http.sslverify "false"

RUN mv /usr/local/etc/php/php.ini-development /usr/local/etc/php/php.ini

RUN echo "upload_max_filesize = 200M" >> /usr/local/etc/php/php.ini
RUN echo "post_max_size = 200M" >> /usr/local/etc/php/php.ini

RUN pwd
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
RUN php -r "if (hash_file('sha384', 'composer-setup.php') === 'dac665fdc30fdd8ec78b38b9800061b4150413ff2e3b6f88543c636f7cd84f6db9189d43a81e5503cda447da73c7e5b6') { echo 'Installer verified'.PHP_EOL; } else { echo 'Installer corrupt'.PHP_EOL; unlink('composer-setup.php'); exit(1); }"
RUN php composer-setup.php
RUN php -r "unlink('composer-setup.php');"
RUN mv composer.phar /usr/local/bin/composer
RUN composer --version

ENV BASH 1
ENV NVM_DIR /root/.nvm
ENV NODE_VERSION 14

RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash \
    && . $NVM_DIR/nvm.sh \
    && nvm install $NODE_VERSION

ENV NODE_PATH $NVM_DIR/v$NODE_VERSION/lib/node_modules
ENV PATH      $NVM_DIR/bin:$NVM_DIR/versions/node/v$NODE_VERSION/bin:$PATH

EXPOSE 9000

CMD ["php-fpm"]
