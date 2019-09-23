FROM php:7
LABEL maintainer="Wesley Elfring <hi@wesleyelfring.nl>"

# Set node version
ENV NODE_VERSION=12.10.0

# Copy the PHP configuration file
COPY ./php.ini /usr/local/etc/php/

# Install Apt transport for node/yarn repo's
RUN apt-get update -yqq \
    && apt-get -yqq install apt-transport-https ca-certificates wget gnupg

# Install, start with Yarn and NodeJS repo keys
RUN curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - \
    && echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list \
    && curl -sL https://deb.nodesource.com/setup_6.x | bash - \
    && apt-get update -yqq \
    && apt-get install -yqq --no-install-recommends \
        # Install libs for building PHP exts
        libicu-dev \
        libpq-dev \
        libmcrypt-dev \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        libpng-dev \
        libzip-dev \
        # Install Build tools
        build-essential \
        nano \
        mariadb-client \
        apt-utils \
        git \
        xvfb \
        unzip \
        wget \
        nodejs \
        yarn \
    && rm -r /var/lib/apt/lists/*
# Install PHP extensions
RUN docker-php-ext-configure pdo_mysql --with-pdo-mysql=mysqlnd \
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
    && docker-php-ext-enable redis xdebug
# Configure PHP
RUN phpmemory_limit=1024M \
    # Increase the PHP Memory limit to 1GB
    && sed -i 's/memory_limit = .*/memory_limit = '${phpmemory_limit}'/' ${PHP_INI_DIR}/php.ini \
    # Install Composer
    && curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
    # Install PHPUnit
    && wget https://phar.phpunit.de/phpunit.phar \
    && chmod +x phpunit.phar && mv phpunit.phar /usr/local/bin/phpunit
    
# Install NVM & Node
RUN curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh | bash
ENV NVM_DIR=/root/.nvm
RUN . "$NVM_DIR/nvm.sh" && nvm install ${NODE_VERSION}
RUN . "$NVM_DIR/nvm.sh" && nvm use v${NODE_VERSION}
RUN . "$NVM_DIR/nvm.sh" && nvm alias default v${NODE_VERSION}
ENV PATH="/root/.nvm/versions/node/v${NODE_VERSION}/bin/:${PATH}"

# Install Global NPM packages
RUN npm install -g n && n stable && npm install -g npm gulp

# Cleanup
RUN apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*
