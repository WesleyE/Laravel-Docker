FROM php:7-apache
LABEL maintainer="Wesley Elfring <hi@wesleyelfring.nl>"

# Copy the PHP configuration file
COPY ./php.ini /usr/local/etc/php/

# Install Apt transport for node/yarn repo's
RUN apt-get update -yqq \
    && apt-get -yqq install apt-transport-https ca-certificates wget gnupg

# Install PHP extensions
RUN apt-get update -yqq \
    # Install libs for building PHP exts
    && apt-get install -yqq --no-install-recommends \
        libicu-dev \
        libpq-dev \
        libmcrypt-dev \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        libpng-dev \
        unzip \
        nano \
        mysql-client \
    # Install PHP Exts
    && docker-php-ext-configure pdo_mysql --with-pdo-mysql=mysqlnd \
    && docker-php-ext-install \
        intl \
        pcntl \
        pdo_mysql \
        pdo_pgsql \
        pgsql \
        zip \
        opcache \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd \
    && pecl install redis \
    && pecl install xdebug \
    && docker-php-ext-enable redis \
    # Remove dev packages
    && apt-get remove --purge -yyq libicu-dev \
        libpq-dev \
        libmcrypt-dev \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        libpng-dev \
    && rm -r /var/lib/apt/lists/*

# Configure PHP

RUN phpmemory_limit=512M \
    # Increase the PHP Memory limit to 512mb for both Apache and the CLI
    && sed -i 's/memory_limit = .*/memory_limit = '${phpmemory_limit}'/' ${PHP_INI_DIR}/php.ini 

# Add apache and php config for Laravel
COPY ./site.conf /etc/apache2/sites-available/site.conf
RUN a2dissite 000-default.conf && a2ensite site.conf && a2enmod rewrite

# Change uid and gid of apache to docker user uid/gid
RUN usermod -u 1000 www-data && groupmod -g 1000 www-data
