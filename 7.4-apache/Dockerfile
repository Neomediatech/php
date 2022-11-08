FROM php:7.4-apache

LABEL maintainer="docker-dario@neomediatech.it" \
      org.label-schema.version=$PHP_VERSION

ENV DEBIAN_FRONTEND=noninteractive \
    TZ=Europe/Rome

RUN echo $TZ > /etc/timezone && \
    apt-get update && apt-get -y dist-upgrade && \
    apt-get install -y libbz2-dev libpng-dev pdfgrep libfcgi-bin libzip-dev netcat tzdata libjpeg-dev libwebp-dev libpq-dev \
                       whois netbase && \
    rm -rf /var/lib/apt/lists*

RUN for mod in mysqli bz2 zip pdo_mysql pdo_pgsql pgsql; do docker-php-ext-install -j$(nproc) $mod ; done && \
    echo "[PHP]" > $PHP_INI_DIR/conf.d/0-custom.ini && \
    echo "memory_limit = 1024M" >> $PHP_INI_DIR/conf.d/0-custom.ini && \
    echo "post_max_size = 200M" >> $PHP_INI_DIR/conf.d/0-custom.ini && \
    echo "upload_max_filesize = 200M" >> $PHP_INI_DIR/conf.d/0-custom.ini && \
    echo "max_execution_time = 300" >> $PHP_INI_DIR/conf.d/0-custom.ini && \
    echo "date.timezone = $TZ" >> $PHP_INI_DIR/conf.d/0-custom.ini && \
    echo "expose_php = Off" >> $PHP_INI_DIR/conf.d/0-custom.ini && \
    echo "upload_tmp_dir = /tmp" >> $PHP_INI_DIR/conf.d/0-custom.ini

# Redis
RUN pecl bundle -d /usr/src/php/ext redis && \
    rm -f /usr/src/php/ext/redis-*.tgz && \
    docker-php-ext-install -j$(nproc) redis

# mailparse
RUN pecl bundle -d /usr/src/php/ext mailparse && \
    rm -f /usr/src/php/ext/mailparse-*.tgz && \
    docker-php-ext-install -j$(nproc) mailparse

# Enable gd support
RUN docker-php-ext-configure gd --with-jpeg --with-webp
RUN docker-php-ext-install -j$(nproc) gd

# Enable EXIF support
RUN docker-php-ext-install -j$(nproc) exif

# Enable INTL, SOAP, XSL, SOCKETS (and others) support (for Magento and Prestashop installations)
RUN apt-get update && \
    apt-get install -y --no-install-recommends libicu-dev libxml2-dev libxslt-dev && \
    rm -rf /var/lib/apt/lists*
RUN docker-php-ext-install -j$(nproc) intl soap xsl sockets bcmath

# Enable IMAP support
RUN apt-get update && \
    apt-get install -y --no-install-recommends libc-client-dev libkrb5-dev && \
    rm -rf /var/lib/apt/lists*
RUN docker-php-ext-configure imap --with-kerberos --with-imap-ssl \
    && docker-php-ext-install -j$(nproc) imap

RUN echo "ServerTokens Prod" > $APACHE_CONFDIR/conf-enabled/zz-custom.conf && \
    echo "ServerSignature Off" >> $APACHE_CONFDIR/conf-enabled/zz-custom.conf && \
    echo '<Location "/server-status">' >> $APACHE_CONFDIR/conf-enabled/zz-custom.conf && \
    echo "  SetHandler server-status" >> $APACHE_CONFDIR/conf-enabled/zz-custom.conf && \
    echo "  Require  host  localhost" >> $APACHE_CONFDIR/conf-enabled/zz-custom.conf && \
    echo "</Location>" >> $APACHE_CONFDIR/conf-enabled/zz-custom.conf 

# Rewrite engine
RUN a2enmod rewrite

HEALTHCHECK --interval=60s --timeout=5s --start-period=60s --retries=10 CMD curl -sS localhost/server-status?auto | grep -E "ServerVersion|RestartTime|ServerUptime:" && echo "Gimme a page" || exit 1
