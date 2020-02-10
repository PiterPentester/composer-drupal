# My alpine base image

FROM alpine:3.10

ENV PHP_V 7
ENV SOFT nginx \
 supervisor \
 wget

ENV PHP_MOD php${PHP_V} \ 
 php${PHP_V}-fpm \
 php${PHP_V}-common \
 php${PHP_V}-mysqli \
 php${PHP_V}-json \
 php${PHP_V}-openssl \
 php${PHP_V}-curl \
 php${PHP_V}-session \
 php${PHP_V}-pdo \
 php${PHP_V}-pdo_mysql \
 php${PHP_V}-dom \
 php${PHP_V}-simplexml \
 php${PHP_V}-xml \
 php${PHP_V}-xmlrpc \
 php${PHP_V}-tokenizer \
 php${PHP_V}-common \
 php${PHP_V}-opcache \
 php${PHP_V}-mbstring \
 php${PHP_V}-gd \
 php${PHP_V}-phar

RUN apk update \
  && apk --no-cache add ${SOFT} ${PHP_MOD} \
  && php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
  && php -r "if (hash_file('sha384', 'composer-setup.php') === 'c5b9b6d368201a9db6f74e2611495f369991b72d9c8cbd3ffbc63edff210eb73d46ffbfce88669ad33695ef77dc76976') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;" \
  && php composer-setup.php --install-dir=/usr/local/bin --filename=composer \
  && php -r "unlink('composer-setup.php');" \
  && cd /var/www/localhost/htdocs/ \
  && wget https://ftp.drupal.org/files/projects/drupal-8.8.2.tar.gz \
  && tar zxvf drupal* \
  && rm *.tar.gz \
  && mv drupal* drupal \
  && cd drupal \
  && composer install -o --no-progress --no-dev --profile --prefer-dist \
  && addgroup -g 1000 user && adduser -u 1000 -D -G user user \
  && mkdir /run/nginx \
  && touch /run/nginx/nginx.pid \
  && touch /run/supervisord.pid \
  && chown -R user:user /var/www/localhost/htdocs/drupal \
  && chown -R user:user /run/nginx/nginx.pid \
  && chown -R user:user /run/supervisord.pid \
  && chown -R user:user /var/lib/nginx \
  && chown -R user:user /var/tmp/nginx \
  && chown -R user:user /var/log/nginx \
  && chown -R user:user /var/log/php7 \
  && rm -rf /var/cache/apk/*

COPY --chown=user test.php /var/www/localhost/htdocs/
COPY --chown=user nginx.conf /etc/nginx/nginx.conf
COPY --chown=user supervisord.conf /

USER user

EXPOSE 8080

ENTRYPOINT ["/usr/bin/supervisord", "-c", "/supervisord.conf"]
