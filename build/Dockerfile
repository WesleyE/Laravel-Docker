FROM php:7
MAINTAINER Wesley Elfring <hi@wesleyelfring.nl>

# Copy the PHP configuration file
COPY ./php.ini /usr/local/etc/php/

# Install, start with Yarn and NodeJS repo keys
RUN curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - \
    && curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash - \
    && apt-get update -yqq \
    && apt-get install -yqq --no-install-recommends \

        # Install libs for building PHP exts
        libicu-dev \
        libpq-dev \
        libmcrypt-dev \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        libpng12-dev \

        # Install Build tools
        build-essential \
        apt-utils \
        git \
        xvfb \
        unzip \
        wget \
        nodejs \
        yarn \
    && rm -r /var/lib/apt/lists/* \
    && docker-php-ext-configure pdo_mysql --with-pdo-mysql=mysqlnd \
    && docker-php-ext-install \
        intl \
        mcrypt \
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
    && docker-php-ext-enable redis xdebug \

    # Increase the PHP Memory limit to 1GB
    && phpmemory_limit=1024M \
    && sed -i 's/memory_limit = .*/memory_limit = '${phpmemory_limit}'/' ${PHP_INI_DIR}/php.ini \

    # Install Composer
    && curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \

    # Install PHPUnit
    && wget https://phar.phpunit.de/phpunit.phar \
    && chmod +x phpunit.phar && mv phpunit.phar /usr/local/bin/phpunit \

    # Install Global NPM packages
    && npm install -g n && n stable && npm install -g npm gulp \

    # Cleanup
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*
