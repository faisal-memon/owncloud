FROM resin/rpi-raspbian:latest

RUN echo "deb http://repozytorium.mati75.eu/raspbian jessie-backports main contrib non-free" > /etc/apt/sources.list.d/php7.list

RUN gpg --keyserver pgpkeys.mit.edu --recv-key CCD91D6111A06851 && \
     gpg --armor --export CCD91D6111A06851 | apt-key add -

RUN apt-get update && apt-get install -y \
	bzip2 \
	libcurl4-openssl-dev \
	libfreetype6-dev \
	libicu-dev \
	libjpeg-dev \
	libmcrypt-dev \
	libmemcached-dev \
	libpng12-dev \
	libpq-dev \
	libxml2-dev \
	curl \
	php7.0 \
	php7.0-fpm \
	php7.0-mysql \
	php7.0-curl \
	php7.0-gd \
	&& rm -rf /var/lib/apt/lists/*
# php5-apcu \

#
# https://doc.owncloud.org/server/8.1/admin_manual/installation/source_installation.html#prerequisites
#RUN docker-php-ext-configure gd --with-png-dir=/usr --with-jpeg-dir=/usr \
#	&& docker-php-ext-install gd exif intl mbstring mcrypt mysql opcache pdo_mysql pdo_pgsql pgsql zip

# set recommended PHP.ini settings
# see https://secure.php.net/manual/en/opcache.installation.php
#RUN { \
#		echo 'opcache.memory_consumption=128'; \
#		echo 'opcache.interned_strings_buffer=8'; \
#		echo 'opcache.max_accelerated_files=4000'; \
#		echo 'opcache.revalidate_freq=60'; \
#		echo 'opcache.fast_shutdown=1'; \
#		echo 'opcache.enable_cli=1'; \
#	} > /usr/local/etc/php/conf.d/opcache-recommended.ini

# PECL extensions
#RUN pecl install APCu-4.0.10 redis memcached \
#	&& docker-php-ext-enable apcu redis memcached

ENV OWNCLOUD_VERSION 8.2.5
VOLUME /var/www/html

RUN curl -k -fsSL -o owncloud.tar.bz2 \
		"https://download.owncloud.org/community/owncloud-${OWNCLOUD_VERSION}.tar.bz2" \
	&& curl -k -fsSL -o owncloud.tar.bz2.asc \
		"https://download.owncloud.org/community/owncloud-${OWNCLOUD_VERSION}.tar.bz2.asc" \
	&& export GNUPGHOME="$(mktemp -d)" \
# gpg key from https://owncloud.org/owncloud.asc
	&& gpg --keyserver ha.pool.sks-keyservers.net --recv-keys E3036906AD9F30807351FAC32D5D5E97F6978A26 \
	&& gpg --batch --verify owncloud.tar.bz2.asc owncloud.tar.bz2 \
	&& rm -r "$GNUPGHOME" owncloud.tar.bz2.asc \
	#&& tar -xjf owncloud.tar.bz2 -C /usr/src/ \
	&& tar -xjf owncloud.tar.bz2 -C /var/www/ \
	&& rm owncloud.tar.bz2

COPY docker-entrypoint.sh /entrypoint.sh

#RUN ln -sf /dev/stdout /var/log/php5-fpm.log

ENTRYPOINT ["/entrypoint.sh"]

RUN mkdir /run/php
#ENTRYPOINT ["/bin/sh"]
#CMD ["php5-fpm", "-F"]
CMD ["php-fpm7.0", "-F"]
#CMD ["bash"]
