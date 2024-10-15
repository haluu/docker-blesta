FROM  alpine:3.20
LABEL Maintainer="Alexander Luu <alexander@luu.no>" \
      Description="Blesta image for Docker - using Nginx, PHP82, ioncube on Alpine Linux"

ENV BLESTA_VERSION=5.10.2 \
		DOWNLOAD_ID=254

ARG GID=1000
ARG UID=1000

# Update Alpine OS
RUN apk update && apk upgrade

# Install packages and remove default server definition
RUN apk add --no-cache \
		bash \
		php82 \
		php82-fpm \
		php82-opcache \
		php82-mysqli \
		php82-json \
		php82-openssl \
		php82-curl \
		php82-gmp \
		php82-zlib \
		php82-xml \
		php82-phar \
		php82-intl \
		php82-dom \
		php82-xmlreader \
		php82-ctype \
		php82-session \
		php82-imap \
		php82-pdo_mysql \
		php82-pdo \
		php82-mbstring \
		php82-gd \
		php82-soap \
		php82-iconv \
		php82-simplexml \
		php82-pecl-mailparse \
		nginx supervisor curl shadow

# Download ioncube
RUN cd /tmp \
		&& curl -sSL https://downloads.ioncube.com/loader_downloads/ioncube_loaders_lin_x86-64.tar.gz -o ioncube.tar.gz \
		&& tar -xf ioncube.tar.gz \
		&& mv ioncube/ioncube_loader_lin_8.2.so /usr/lib/php82/modules/ioncube_loader_lin_8.2.so \
		&& echo 'zend_extension = /usr/lib/php82/modules/ioncube_loader_lin_8.2.so' > /etc/php82/conf.d/00-ioncube.ini \
		&& rm ioncube.tar.gz

# Copy configs
COPY config/nginx.conf /etc/nginx/nginx.conf
COPY config/fpm-pool.conf /etc/php82/php-fpm.d/www.conf
COPY config/php.ini /etc/php82/conf.d/custom.ini
COPY config/supervisord.conf /etc/supervisor/conf.d/supervisord.conf

# Change working directory
WORKDIR /var/www

# Create root directory
RUN mkdir -p /var/www/html

# Add Blesta's cron
RUN echo '*/5 * * * * nobody /usr/local/bin/php /var/www/app/blesta/index.php cron' > /etc/crontabs/blesta

# Change UID and GID of nobody to 1000 to match most host user's IDs
RUN usermod -u ${UID} nobody && groupmod -g ${GID} nobody

# Run as non-root user
RUN chown -R nobody.nobody /var/www \
		&& chown -R nobody.nobody /run \
		&& chown -R nobody.nobody /var/lib/nginx \
		&& chown -R nobody.nobody /var/log/nginx

# Switch to non-root user
USER nobody

# Download blesta
RUN curl -Lo blesta.zip https://account.blesta.com/client/plugin/download_manager/client_main/download/${DOWNLOAD_ID} \
		&& unzip blesta.zip -q \
		&& rm blesta.zip

# Expose the port
EXPOSE 80

# Let supervisord start nginx & php-fpm
CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]

# Healthcheck
HEALTHCHECK --timeout=10s CMD curl --silent --fail http://127.0.0.1/fpm-ping
