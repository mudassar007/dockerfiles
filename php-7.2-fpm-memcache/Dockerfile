FROM php:7.2-fpm

RUN pecl install mysql redis

# Install modules
RUN apt-get update && apt-get install -y  --no-install-recommends \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    libpq-dev \
    libcurl4-openssl-dev \
    libxml2-dev \
    apt-utils \
    mysql-client \
    zlib1g-dev \
    git \
    imagemagick \
    unzip \
    mailutils \
    ssmtp

RUN docker-php-ext-install pdo_mysql mbstring opcache zip curl \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install gd \
    && docker-php-ext-install exif \
    && docker-php-ext-install soap


# Install required RabbitMQ modules
RUN docker-php-ext-install -j$(nproc) bcmath

# imagemagick as PHP extension
RUN apt-get install -y libmagickwand-6.q16-dev --no-install-recommends \
 && pecl install imagick \
 && echo "extension=imagick.so" > /usr/local/etc/php/conf.d/ext-imagick.ini


# Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
RUN composer --version

# Install Drush.
RUN composer global require drush/drush:7.*
ADD profile-extras.sh /etc/profile.d/profile-extras.sh

# Set timezone
RUN rm /etc/localtime
RUN ln -s /usr/share/zoneinfo/Europe/Copenhagen /etc/localtime
RUN "date"

#RUN pecl install memcached  && docker-php-ext-enable memcached
# Install memcache extension
RUN set -x \
    && apt-get update && apt-get install -y --no-install-recommends unzip libssl-dev libpcre3 libpcre3-dev \
    && cd /tmp \
    && curl -sSL -o php7.zip https://github.com/websupport-sk/pecl-memcache/archive/php7.zip \
    && unzip php7 \
    && cd pecl-memcache-php7 \
    && /usr/local/bin/phpize \
    && ./configure --with-php-config=/usr/local/bin/php-config \
    && make \
    && make install \
    && echo "extension=memcache.so" > /usr/local/etc/php/conf.d/ext-memcache.ini \
    && rm -rf /tmp/pecl-memcache-php7 php7.zip
    

# Type docker-php-ext-install to see available extensions
RUN docker-php-ext-install pdo pdo_mysql
RUN echo "extension=redis.so" > /usr/local/etc/php/conf.d/redis.ini 

ADD entrypoint.sh /usr/local/bin/entrypoint.sh
RUN chmod +x /usr/local/bin/entrypoint.sh
ENTRYPOINT ["/usr/local/bin/entrypoint.sh"]

