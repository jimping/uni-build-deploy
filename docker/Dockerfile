###############################################################################
# Node JS
###############################################################################

FROM splintnet/alpine-node:latest as nodejs

# Install NPM Packages
COPY package*.json yarn.lock ./

RUN yarn install

COPY .env .styleci.yml tailwind.config.js webpack.mix.js ./
COPY resources ./resources
COPY public ./public

RUN yarn prod

RUN rm -f .env

###############################################################################
# Nginx
###############################################################################

FROM nginx:alpine as nginx

WORKDIR /application

COPY docker/config/nginx.conf /etc/nginx/conf.d/default.conf
COPY --chown=nginx:nginx ./public /application/public
COPY --chown=nginx:nginx --from=nodejs /application/public /application/public

###############################################################################
# PHP
###############################################################################

FROM splintnet/alpine-php as php

COPY --chown=www-data:www-data composer.json composer.lock ./

RUN chown www-data:www-data .
USER www-data
RUN composer install --no-scripts --no-autoloader

COPY --chown=www-data:www-data . .
COPY --chown=www-data:www-data --from=nodejs /application/public /application/public

RUN composer dump-autoload
