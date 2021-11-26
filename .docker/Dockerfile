FROM nginx:mainline-alpine

COPY start.sh /start.sh
COPY nginx.conf /etc/nginx/nginx.conf
COPY supervisord.conf /etc/supervisord.conf
COPY site.conf /etc/nginx/sites-available/default.conf

RUN apk add --update \
    php8 \
    php8-fpm \
    php8-pdo \
    php8-pdo_mysql \
    php8-mbstring \
    php8-xml \
    php8-openssl \
    php8-json \
    php8-phar \
    php8-zip \
    php8-dom \
    php8-session \
    php8-tokenizer \
    php8-zlib && \
    php8 -r "copy('http://getcomposer.org/installer', 'composer-setup.php');" && \
    php8 composer-setup.php --install-dir=/usr/bin --filename=composer && \
    php8 -r "unlink('composer-setup.php');" && \
    ln -s /usr/bin/php8 /usr/bin/php && \
    ln -s /etc/php8/php.ini /etc/php8/conf.d/php.ini

RUN apk add --update \
    bash \
    openssh-client \
    supervisor

RUN mkdir -p /etc/nginx && \
    mkdir -p /etc/nginx/sites-available && \
    mkdir -p /etc/nginx/sites-enabled && \
    mkdir -p /run/nginx && \
    ln -s /etc/nginx/sites-available/default.conf /etc/nginx/sites-enabled/default.conf && \
    mkdir -p /var/log/supervisor && \
    rm -Rf /var/www/* && \
    chmod 755 /start.sh

RUN sed -i -e "s/;cgi.fix_pathinfo=1/cgi.fix_pathinfo=0/g" \
    -e "s/variables_order = \"GPCS\"/variables_order = \"EGPCS\"/g" \
    /etc/php8/php.ini && \
    sed -i -e "s/;daemonize\s*=\s*yes/daemonize = no/g" \
    -e "s/;catch_workers_output\s*=\s*yes/catch_workers_output = yes/g" \
    -e "s/user = nobody/user = nginx/g" \
    -e "s/group = nobody/group = nginx/g" \
    -e "s/;listen.mode = 0660/listen.mode = 0666/g" \
    -e "s/;listen.owner = nobody/listen.owner = nginx/g" \
    -e "s/;listen.group = nobody/listen.group = nginx/g" \
    -e "s/listen = 127.0.0.1:9000/listen = \/var\/run\/php-fpm.sock/g" \
    -e "s/^;clear_env = no$/clear_env = no/" \
    /etc/php8/php-fpm.d/www.conf

EXPOSE 443 80
WORKDIR /var/www

CMD ["/start.sh"]
