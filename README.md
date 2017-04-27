# laravel-apache
Docker image for Laravel based on Apache

## Base Image
Based on _/php:7apache, see https://hub.docker.com/_/php/

## Extensions
The following extensions are installed out of the box

- intl
- mbstring
- mcrypt
- pcntl
- pdo_mysql
- pdo_pgsql
- pgsql
- zip
- opcache
- gd
- redis (PECL)
- *xdebug (PECL)*

Note that xdebug is not enabled. Enable it with `RUN docker-php-ext-enable xdebug`

## Using this image
Make the following Dockerfile in your project:
```
FROM wesleyelfring/laravel-apache:latest
MAINTAINER Wesley Elfring <hi@wesleyelfring.nl>

# Copy files
WORKDIR /var/www/html/
COPY . .

```

### Overwriting Apache vhost config

You can overwrite the site.conf by adding the following to your Dockerfile:
```
# Write apache config
COPY sites.conf /etc/apache2/sites-available/site.conf
RUN a2ensite sites.conf
```
The default contents of site.conf are:
```
<VirtualHost *:80>
  DocumentRoot /var/www/html/public

  <Directory /var/www/html/public>
    AllowOverride All
  </Directory>
</VirtualHost>
```

*Keep in mind that you might want to log to stdout and stderr to see the logs in Docker. In that case, do not add log statements in your Apache config*
