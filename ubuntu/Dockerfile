#
# Базовый образ (ubuntu_base)
#
FROM ubuntu:22.04 AS ubuntu_base

# Отключаем интерактивный режим при установке пакетов
ENV DEBIAN_FRONTEND=noninteractive

# Устанавливаем нужные пакеты и репозиторий ppa:ondrej/php
RUN apt-get update \
    && apt-get install -yq software-properties-common \
    && add-apt-repository universe \
    && add-apt-repository ppa:ondrej/php \
    && apt-get update \
    && apt-get install -yq --no-install-recommends \
       apt-utils \
       zip \
       unzip \
       curl \
       git \
       apache2 \
       libapache2-mod-php8.2 \
       php8.2-cli \
       php8.2-curl \
       php8.2-fpm \
       php8.2-gd \
       php8.2-ldap \
       php8.2-mbstring \
       php8.2-mysql \
       php8.2-soap \
       php8.2-sqlite3 \
       php8.2-xml \
       php8.2-zip \
       php8.2-intl \
       php8.2-bcmath \
       php-pear \
       php-imagick \
       cron \
       openssl \
       nano \
       graphicsmagick \
       imagemagick \
       ghostscript \
       mysql-client \
       iputils-ping \
       locales \
       ca-certificates \
       ssl-cert \
       wget \
       gnupg \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Установка Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Генерация нужных локалей
RUN locale-gen en_US.UTF-8 en_GB.UTF-8 de_DE.UTF-8 es_ES.UTF-8 fr_FR.UTF-8 it_IT.UTF-8 km_KH sv_SE.UTF-8 fi_FI.UTF-8 ru_RU.UTF-8

# Включаем модули Apache
RUN a2enmod rewrite expires deflate ssl headers

# Параметры для создания/переопределения пользователя
ARG HTTP_USER
ARG APACHE_USER_GID
ARG APACHE_USER_UID

RUN if [ -n "${HTTP_USER}" ]; then \
      if [ ! "$(getent passwd "${HTTP_USER}")" ]; then \
        groupadd -o -g ${APACHE_USER_GID:-33} ${HTTP_USER} \
          && useradd -o -g${APACHE_USER_GID:-33} -u${APACHE_USER_UID:-33} -Gwww-data -m -s/bin/bash ${HTTP_USER}; \
      fi; \
      if [ "${HTTP_USER}" != "root" ]; then \
        sed -i "s/=www-data/=${HTTP_USER}/g" /etc/apache2/envvars; \
      fi; \
      # Настройка Mutex, если нужно
      sed -i '/^#Mutex file.*/a Mutex posixsem' /etc/apache2/apache2.conf; \
    fi

# Открываем порты
EXPOSE 80
EXPOSE 443

# Рабочая директория
WORKDIR /var/www

# Команда по умолчанию
CMD ["apachectl", "-D", "FOREGROUND"]


#
# Дополнительный стейдж для разработки (ubuntu_dev)
#
FROM ubuntu_base AS ubuntu_dev

ENV DEFAULT_EMAIL_FROM=noreply@example.com

# Устанавливаем xdebug и msmtp
RUN apt-get update \
    && apt-get install -yq --no-install-recommends \
       php8.2-xdebug \
       msmtp \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

