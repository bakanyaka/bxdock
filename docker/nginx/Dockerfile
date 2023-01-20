FROM alpine:edge

LABEL maintainer="Dmitriy Belyakov"

# for shadow package
RUN echo http://dl-2.alpinelinux.org/alpine/edge/community/ >> /etc/apk/repositories

# shadow for usermod
RUN apk add --no-cache nginx-mod-http-brotli shadow

COPY conf/nginx.conf /etc/nginx/
COPY conf/default.conf /etc/nginx/conf.d/
COPY conf/upstream.conf /etc/nginx/conf.d/

RUN usermod -u 1000 nginx
RUN groupmod -g 1000 nginx

# run nginx with configuration reload once in every 6 hours
CMD /bin/sh -c 'while :; do /bin/sleep 6h & wait ${!}; /usr/sbin/nginx -s reload; done & /usr/sbin/nginx -g "daemon off;"'
