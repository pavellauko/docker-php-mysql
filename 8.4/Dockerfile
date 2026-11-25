FROM php:8.4.28-apache
RUN a2enmod rewrite && a2enmod headers

# Silences "Could not reliably determine the server's fully qualified domain name"
RUN echo 'ServerName localhost' > /etc/apache2/conf-available/servername.conf && \
    a2enconf servername

RUN apt-get update && apt-get install -y \
    vim \
    git \
    curl \
    zip \
    unzip \
    zlib1g-dev \
    libzip-dev \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    libpng-dev \
    libwebp-dev \
    libicu-dev \
    sendmail \
    && rm -rf /var/lib/apt/lists/*

# composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# sendmail
RUN echo "sendmail_path=/usr/sbin/sendmail -t -i" >> /usr/local/etc/php/conf.d/sendmail.ini
RUN sed -i '/#!\/bin\/sh/aservice sendmail restart' /usr/local/bin/docker-php-entrypoint
RUN sed -i '/#!\/bin\/sh/aecho "$(hostname -i)\t$(hostname) $(hostname).localhost" >> /etc/hosts' /usr/local/bin/docker-php-entrypoint

# GD (with WebP)
RUN docker-php-ext-configure gd --with-freetype --with-jpeg --with-webp && \
    docker-php-ext-install gd

# php database
RUN docker-php-ext-install pdo_mysql && \
    docker-php-ext-install mysqli && \
    docker-php-ext-enable mysqli && \
    docker-php-ext-configure intl && \
    docker-php-ext-install intl

# php libs
# exif - photo orientation from phones; bcmath - arbitrary precision math (e.g. google/photos-library)
# OPcache is already enabled by the official php:8.4 image.
RUN docker-php-ext-install zip exif bcmath
