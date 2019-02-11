FROM  php:7.0-apache
LABEL MAINTAINER juanda <juandacorreo@gmail.com>

# Moodle version without . (36 means 3.6)
ARG VERSION=36

VOLUME ["/var/moodledata"]
EXPOSE 80

# Let the container know that there is no tty JUST WHEN INSTALLING!
# Later we can run docker -ti and we need interactivity

ARG DEBIAN_FRONTEND=noninteractive

## extensions based on https://github.com/moodlehq/moodle-php-apache/blob/master/Dockerfile
COPY ./php-extensions.sh /tmp
RUN /tmp/php-extensions.sh


# set recommended PHP.ini settings
# see https://secure.php.net/manual/en/opcache.installation.php
RUN { \
		echo 'opcache.memory_consumption=128'; \
		echo 'opcache.interned_strings_buffer=8'; \
		echo 'opcache.max_accelerated_files=4000'; \
		echo 'opcache.revalidate_freq=2'; \
		echo 'opcache.fast_shutdown=1'; \
		echo 'opcache.enable_cli=1'; \
	} > /usr/local/etc/php/conf.d/opcache-recommended.ini

# usefull for moodle

RUN { \
	echo 'file_uploads = On'; \
	echo 'memory_limit = 256M'; \
	echo 'upload_max_filesize = 64M'; \
	echo 'post_max_size = 64M'; \
	echo 'max_execution_time = 600'; \
} > /usr/local/etc/php/conf.d/uploads.ini

RUN a2enmod rewrite expires

# RUN	echo "Installing moodle" && \
# 		# better tgz file, smaller and we can remove parent directory with --strip option so we don't have to move folder, time saving!!!
# 		curl https://download.moodle.org/download.php/direct/stable${VERSION}/moodle-latest-${VERSION}.tgz -o /var/www/html/moodle-latest.tgz  && \
# 		cd /var/www/html && tar -xf moodle-latest.tgz --strip 1 && \
# 		mkdir /var/www/moodledata && chown www-data:www-data /var/www/moodledata  && \
# 		chown -R www-data:www-data -R /var/www/html && \
# 		rm /var/www/html/moodle-latest.tgz

# Fix the original permissions of /tmp, the PHP default upload tmp dir.
RUN chmod 777 /tmp && chmod +t /tmp

WORKDIR /home

RUN apt update
RUN apt install software-properties-common -y
RUN apt update
RUN apt install git -y
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
RUN php -r "if (hash_file('sha384', 'composer-setup.php') === '48e3236262b34d30969dca3c37281b3b4bbe3221bda826ac6a9a62d6444cdb0dcd0615698a5cbe587c3f0fe57a54d8f5') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
RUN php composer-setup.php
RUN php -r "unlink('composer-setup.php');"
RUN mv composer.phar /usr/local/bin/composer
RUN git clone git://github.com/tmuras/moosh.git
WORKDIR /home/moosh
RUN composer install
RUN ln -s $PWD/moosh.php /usr/local/bin/moosh

WORKDIR /var/www/html