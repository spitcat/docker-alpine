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

# Install mhsendmail
RUN apk update && apk add \
     go \
     git && \
     mkdir /root/go && \
    GOPATH=/root/go && \
    PATH=$PATH:$GOPATH/bin && \
    go get github.com/mailhog/mhsendmail && \
    cp /root/go/bin/mhsendmail /usr/local/bin/mhsendmail