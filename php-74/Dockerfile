FROM php:7.4-fpm

ENV APP_HOME /var/www/html
ARG USER_ID=1000
ARG GROUP_ID=1000

# reset www-data uuid for matching host user uuid

RUN userdel -f www-data &&\
    if getent group www-data ; then groupdel www-data; fi &&\
    groupadd -g ${GROUP_ID} www-data &&\
    useradd -l -u ${USER_ID} -g www-data www-data

# install all the dependencies and enable PHP modules
RUN rm -rf /var/lib/apt/lists/partial && apt-get update -o Acquire::CompressionTypes::Order::=gz \
    && apt-get update && apt-get upgrade -y && apt-get install -y \
      procps \
      nano \
      git \
      unzip \
      zip \
      libicu-dev \
      zlib1g-dev \
      libxml2 \
      libxml2-dev \
      libreadline-dev \
      libzip-dev \
      libpq-dev \
      libpng-dev \
      pear-channels \
      supervisor \
      cron \
    && pecl install redis \
    && docker-php-ext-enable redis \
    && docker-php-ext-configure pdo_mysql --with-pdo-mysql=mysqlnd \
    && docker-php-ext-configure pgsql -with-pgsql=/usr/local/pgsql \
    && docker-php-ext-install \
      pdo_mysql \
      pdo \
      pdo_pgsql \
      pgsql \
      bcmath \
      ctype \
      json \
      mbstring \
      tokenizer \
      xml \
      intl \
      pcntl \
      xmlrpc \
      soap \
      gd \
      zip && \
      rm -fr /tmp/* && \
      rm -rf /var/list/apt/* && \
      rm -r /var/lib/apt/lists/* && \
      apt-get clean

# create document root
RUN mkdir -p $APP_HOME/public

# change owner
RUN chown -R www-data:www-data $APP_HOME

# put php config dir for Laravel
RUN mkdir -p /usr/local/etc/php-fpm.d/

# install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/bin/ --filename=composer

# add supervisor
RUN mkdir -p /var/log/supervisor
RUN mkdir -p /var/spool/cron/crontabs/


# set working directory
WORKDIR $APP_HOME

# create composer folder for user www-data
RUN mkdir -p /var/www/.composer && chown -R www-data:www-data /var/www/.composer

USER www-data
