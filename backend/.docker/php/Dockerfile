ARG PHP_VERSION
ARG PHP_TYPE
FROM php:${PHP_VERSION}-${PHP_TYPE} as dev

COPY --from=composer:latest /usr/bin/composer /usr/bin/composer
COPY --from=mlocati/php-extension-installer /usr/bin/install-php-extensions /usr/bin/
RUN install-php-extensions bcmath gd intl memcached opcache pcntl pdo_pgsql pgsql soap xdebug zip

# Add packages
RUN apt update && apt install -y --no-install-recommends git openssh-client wget sudo unzip
RUN wget https://get.symfony.com/cli/installer -O - | sudo bash && sudo mv /root/.symfony/bin/symfony /usr/local/bin/symfony
# Пока не пофиксят IDE и прочее под xdebug 3.0 ставим xdebug-2.9.8 принудительно
#RUN pecl uninstall xdebug && pecl install xdebug-2.9.8

# Add User
ARG PUID
ARG PGID
ARG USER=dev

RUN set -xe; \
    groupadd -g ${PGID} dev && \
    useradd -l -u ${PUID} -g dev -m ${USER} -G sudo && \
    usermod ${USER} -s /bin/bash
RUN echo "${USER} ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

USER ${USER}

RUN sudo mv "$PHP_INI_DIR/php.ini-development" "$PHP_INI_DIR/php.ini"

WORKDIR "/var/www/app"
EXPOSE 9000
