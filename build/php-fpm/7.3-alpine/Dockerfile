FROM php:7.3-alpine3.11

ENV MY_USER="giant" \
	MY_GROUP="giant" \
	MY_UID="1000" \
	MY_GID="1000" 

RUN set -x \
	&& addgroup -g ${MY_GID} ${MY_GROUP} \
	&& adduser -u ${MY_UID} --system --shell /bin/bash ${MY_USER} -G ${MY_GROUP}  \
	&& rm -rf /usr/local/etc/php-fpm.d \
	&& mkdir -p /usr/local/etc/php-fpm.d \
	&& mkdir -p /var/lib/php/session \
	&& mkdir -p /var/lib/php/wsdlcache \
	&& chown -R ${MY_USER}:${MY_GROUP} /var/lib/php/session \
	&& chown -R ${MY_USER}:${MY_GROUP} /var/lib/php/wsdlcache    


# Install mssql odbc drivers

# Install mhsendmail
RUN apk update && apk add \
	go \
	git \
	gnupg \
	bash \
	freetds-dev \
	unixodbc-dev \
	imagemagick-dev \
	freetype libpng \
	libjpeg-turbo \
	freetype-dev \
	libpng-dev \ 
	libjpeg-turbo-dev \
	php7-dev \
	libssh-dev \
	rabbitmq-c \
	rabbitmq-c-dev \
	curl
RUN 	mkdir /root/go && \
    GOPATH=/root/go && \
    PATH=$PATH:$GOPATH/bin && \
    go get github.com/mailhog/mhsendmail && \
    cp /root/go/bin/mhsendmail /usr/local/bin/mhsendmail

#Download the desired package(s)
RUN curl -O https://download.microsoft.com/download/e/4/e/e4e67866-dffd-428c-aac7-8d28ddafb39b/msodbcsql17_17.6.1.1-1_amd64.apk
RUN curl -O https://download.microsoft.com/download/e/4/e/e4e67866-dffd-428c-aac7-8d28ddafb39b/mssql-tools_17.6.1.1-1_amd64.apk
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.37.2/install.sh | bash


#(Optional) Verify signature, if 'gpg' is missing install it using 'apk add gnupg':
RUN curl -O https://download.microsoft.com/download/e/4/e/e4e67866-dffd-428c-aac7-8d28ddafb39b/msodbcsql17_17.6.1.1-1_amd64.sig
RUN curl -O https://download.microsoft.com/download/e/4/e/e4e67866-dffd-428c-aac7-8d28ddafb39b/mssql-tools_17.6.1.1-1_amd64.sig

RUN  curl https://packages.microsoft.com/keys/microsoft.asc  | gpg --import -
RUN  gpg --verify msodbcsql17_17.6.1.1-1_amd64.sig msodbcsql17_17.6.1.1-1_amd64.apk
RUN  gpg --verify mssql-tools_17.6.1.1-1_amd64.sig mssql-tools_17.6.1.1-1_amd64.apk


#Install the package(s)
RUN apk add --allow-untrusted msodbcsql17_17.6.1.1-1_amd64.apk
RUN apk add --allow-untrusted mssql-tools_17.6.1.1-1_amd64.apk	

# install php extensions

RUN docker-php-ext-configure gd \
	--with-gd \
 	--with-png-dir=/usr/include/ \
    --with-jpeg-dir=/usr/include/  \
	--with-freetype-dir=/usr/include/ \
    # do important things \
	&& NPROC=$(grep -c ^processor /proc/cpuinfo 2>/dev/null || 1) \
  	&& docker-php-ext-install -j${NPROC} gd 

#APCU
RUN apk add --update --no-cache --virtual .build-dependencies $PHPIZE_DEPS \
#		&& pecl channel-update pecl.php.net \
        && pecl install apcu \
        && docker-php-ext-enable apcu \
#        && pecl clear-cache \
        && apk del .build-dependencies
		
# imagick
RUN apk add --update --no-cache autoconf g++ imagemagick-dev libtool make pcre-dev \
#    && pecl channel-update pecl.php.net \
	&&  pecl install imagick \
    && docker-php-ext-enable imagick \
    && apk del autoconf g++ libtool make pcre-dev		

# AMQP
RUN set -eux \
#	&& pecl channel-update pecl.php.net \
	&& pecl install amqp \
	# Enabling
	&& docker-php-ext-enable amqp \
	&& true

# BCMATH
RUN set -eux \
	&& docker-php-ext-install -j$(getconf _NPROCESSORS_ONLN) bcmath \
	&& true

# -------------------- Installing PHP Extension: bz2 --------------------
RUN set -eux \
	# Installation: Generic
	# Type:         Built-in extension
	&& docker-php-ext-install -j$(getconf _NPROCESSORS_ONLN) bz2 \
	&& true


# -------------------- Installing PHP Extension: calendar --------------------
RUN set -eux \
	# Installation: Generic
	# Type:         Built-in extension
	&& docker-php-ext-install -j$(getconf _NPROCESSORS_ONLN) calendar \
	&& true


# -------------------- Installing PHP Extension: dba --------------------
RUN set -eux \
	# Installation: Generic
	# Type:         Built-in extension
	&& docker-php-ext-install -j$(getconf _NPROCESSORS_ONLN) dba \
	&& true


# -------------------- Installing PHP Extension: enchant --------------------
RUN set -eux \
	# Installation: Generic
	# Type:         Built-in extension
	&& docker-php-ext-install -j$(getconf _NPROCESSORS_ONLN) enchant \
	&& true


# -------------------- Installing PHP Extension: exif --------------------
RUN set -eux \
	# Installation: Generic
	# Type:         Built-in extension
	&& docker-php-ext-install -j$(getconf _NPROCESSORS_ONLN) exif \
	&& true


# -------------------- Installing PHP Extension: gd --------------------
# RUN set -eux \
# 	# Generic pre-command
# 	&& ln -s /usr/lib/x86_64-linux-gnu/libXpm.* /usr/lib/ \
# 	# Installation: Version specific
# 	# Type:         Built-in extension
# 	# Custom:       configure command
# 	&& docker-php-ext-configure gd --with-gd --with-webp-dir=/usr --with-jpeg-dir=/usr --with-png-dir=/usr --with-zlib-dir=/usr --with-xpm-dir=/usr --with-freetype-dir=/usr \
# 	# Installation
# 	&& docker-php-ext-install -j$(getconf _NPROCESSORS_ONLN) gd \
# 	&& true


# -------------------- Installing PHP Extension: gettext --------------------
RUN set -eux \
	# Installation: Generic
	# Type:         Built-in extension
	&& docker-php-ext-install -j$(getconf _NPROCESSORS_ONLN) gettext \
	&& true


# -------------------- Installing PHP Extension: gmp --------------------
RUN set -eux \
	# Generic pre-command
	# && ln /usr/include/x86_64-linux-gnu/gmp.h /usr/include/ \
	# Installation: Generic
	# Type:         Built-in extension
	&& docker-php-ext-install -j$(getconf _NPROCESSORS_ONLN) gmp \
	&& true


# -------------------- Installing PHP Extension: igbinary --------------------
RUN set -eux \
	# Installation: Generic
	# Type:         PECL extension
	# Default:      Pecl command
	&& pecl install igbinary \
	# Enabling
	&& docker-php-ext-enable igbinary \
	&& true


###
### Copy configure files
###
# COPY ./config/php-ini.d/php-7.3.ini /usr/local/etc/php/conf.d/xxx-devilbox-default-php.ini
# COPY ./config/php-fpm.conf/php-fpm-7.3.conf /usr/local/etc/php-fpm.conf

# COPY ./config/docker-entrypoint.sh /docker-entrypoint.sh
# COPY ./config/docker-entrypoint.d /docker-entrypoint.d