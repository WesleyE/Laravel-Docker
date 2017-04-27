# Laravel
Complete Docker image for Laravel based on Apache. Configurable to only have what you need.

## Base Image
Based on _/php:7apache and php:7, see https://hub.docker.com/_/php/

## Image Types
| Image Tag | Description                                                                        |
|-----------|------------------------------------------------------------------------------------|
| latest    | Basic image with Apache for Laravel Production sites                               |
| build     | Basic CLI image with building NodeJS (stable via N), Yarn, Gulp, PHPUnit, Composer |
| complete  | Takes the production image and adds the build tools on top                         |

*Both Complete and the Latest images use the official PHP production config, the build image uses the official development config file.*

## Extensions
The following extensions are installed out of the box for the production image

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

Note that xdebug is not enabled in the production (latest) build. Enable it with `RUN docker-php-ext-enable xdebug`. Xdebug is enabled for both the complete and build versions.

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
