FROM alpine:latest

MAINTAINER Tee Tanawatanakul <wallsfantasy@gmail.com>

RUN apk add --no-cache --repository http://dl-cdn.alpinelinux.org/alpine/latest-stable/community/ \
    php7 php7-ctype php7-curl php7-dom php7-iconv php7-intl php7-json php7-mbstring php7-openssl php7-pdo_pgsql \
    php7-posix php7-session php7-tokenizer php7-xdebug

WORKDIR /var/www

CMD ["/bin/sh"]
