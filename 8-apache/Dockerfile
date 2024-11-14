FROM php:8-apache

LABEL maintainer="docker-dario@neomediatech.it" \
      org.label-schema.version=$PHP_VERSION

ENV DEBIAN_FRONTEND=noninteractive \
    TZ=Europe/Rome

RUN echo $TZ > /etc/timezone && \
    apt-get update && apt-get -y dist-upgrade && \
    apt-get install -y pdfgrep libfcgi-bin netcat-traditional tzdata \
                       whois netbase gettext-base wget && \
    rm -rf /var/lib/apt/lists*

ADD --chmod=0755 https://github.com/mlocati/docker-php-extension-installer/releases/latest/download/install-php-extensions /usr/local/bin/

# Install extensions with install-php-extensions, thanks to Michele Locati https://github.com/mlocati
RUN install-php-extensions \
    apcu \
    bcmath \
    bz2 \
    exif \
    gd \
    imap \
    intl \
    mailparse \
    mysqli \
    opcache \
    pdo_mysql \
    redis \
    soap \
    sockets \
    xsl \
    zip \
    imagick/imagick@28f27044e435a2b203e32675e942eb8de620ee58 \
    gmp \
    sysvsem
# For imagick see https://github.com/Imagick/imagick/issues/640#issuecomment-2372464340 for details

# Enable mod_security
RUN apt-get update && \
    apt-get install -y --no-install-recommends libapache2-mod-security2 && \
    rm -rf /var/lib/apt/lists* && \
    a2enmod security2 && \
    echo 'SecServerSignature " "' >> $APACHE_CONFDIR/conf-enabled/zz-custom.conf

# Enable rewrite,  ssl, info, remoteip modules
RUN a2enmod rewrite ssl info remoteip

# Create custom snippets directory
RUN mkdir -p $APACHE_CONFDIR/conf-custom && \
    echo "# Include custom snippets directory:" >> $APACHE_CONFDIR/apache2.conf && \
    echo "IncludeOptional conf-custom/*.conf" >> $APACHE_CONFDIR/apache2.conf

RUN echo "[PHP]" > $PHP_INI_DIR/conf.d/0-custom.ini && \
    echo "memory_limit = 1024M" >> $PHP_INI_DIR/conf.d/0-custom.ini && \
    echo "post_max_size = 200M" >> $PHP_INI_DIR/conf.d/0-custom.ini && \
    echo "upload_max_filesize = 200M" >> $PHP_INI_DIR/conf.d/0-custom.ini && \
    echo "max_execution_time = 300" >> $PHP_INI_DIR/conf.d/0-custom.ini && \
    echo "date.timezone = $TZ" >> $PHP_INI_DIR/conf.d/0-custom.ini && \
    echo "expose_php = Off" >> $PHP_INI_DIR/conf.d/0-custom.ini && \
    echo "upload_tmp_dir = /tmp" >> $PHP_INI_DIR/conf.d/0-custom.ini

RUN echo "ServerTokens Prod" > $APACHE_CONFDIR/conf-enabled/zz-custom.conf && \
    echo "ServerSignature Off" >> $APACHE_CONFDIR/conf-enabled/zz-custom.conf && \
    echo '<Location "/server-status">' >> $APACHE_CONFDIR/conf-enabled/zz-custom.conf && \
    echo "  SetHandler server-status" >> $APACHE_CONFDIR/conf-enabled/zz-custom.conf && \
    echo "  Require  host  localhost" >> $APACHE_CONFDIR/conf-enabled/zz-custom.conf && \
    echo "</Location>" >> $APACHE_CONFDIR/conf-enabled/zz-custom.conf 

# setup some ENV vars to allow customi[s|z]ations
ENV APACHE_START_SERVERS="5" \
    APACHE_MIN_SPARE_SERVERS="5" \
    APACHE_MAX_SPARE_SERVERS="10" \
    APACHE_MAX_REQUEST_WORKERS="150" \
    APACHE_MAX_CONNECTIONS_PER_CHILD="0" \
    APACHE_SERVER_LIMIT="256"

COPY --chmod=444 mpm_prefork.conf.template $APACHE_CONFDIR/

COPY --chmod=755 custom-entrypoint /

ENTRYPOINT ["/custom-entrypoint"]
CMD ["apache2-foreground"]

HEALTHCHECK --interval=60s --timeout=5s --start-period=60s --retries=10 CMD curl -sS localhost/server-status?auto | grep -E "ServerVersion|RestartTime|ServerUptime:" && echo "Gimme a page" || exit 1

