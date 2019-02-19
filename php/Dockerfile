FROM php:5.6-fpm

RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        curl \
        libmemcached-dev \
        libz-dev \
        libpq-dev \
        libjpeg-dev \
        libpng-dev \
        libfreetype6-dev \
        libssl-dev \
        libmcrypt-dev \
        vim

#####################################
# ORACLE:
#####################################
# Install the Oracle Instant Client
ADD oracle-instantclient12.1-basic_12.1.0.2.0-2_amd64.deb /tmp
ADD oracle-instantclient12.1-devel_12.1.0.2.0-2_amd64.deb /tmp
ADD oracle-instantclient12.1-sqlplus_12.1.0.2.0-2_amd64.deb /tmp
RUN dpkg -i /tmp/oracle-instantclient12.1-basic_12.1.0.2.0-2_amd64.deb
RUN dpkg -i /tmp/oracle-instantclient12.1-devel_12.1.0.2.0-2_amd64.deb
RUN dpkg -i /tmp/oracle-instantclient12.1-sqlplus_12.1.0.2.0-2_amd64.deb
RUN rm -rf /tmp/oracle-instantclient12.1-*.deb

RUN apt-get install libaio-dev -y
RUN echo 'instantclient,/usr/local/instantclient' | pecl install oci8

# Install the PHP mcrypt extention
RUN docker-php-ext-install mcrypt

# Install the PHP pdo_mysql extention
RUN docker-php-ext-install mysqli \
    && docker-php-ext-install pdo_mysql

#####################################
# gd:
#####################################

# Install the PHP gd library
RUN docker-php-ext-install gd && \
    docker-php-ext-configure gd \
        --enable-gd-native-ttf \
        --with-jpeg-dir=/usr/lib \
        --with-freetype-dir=/usr/include/freetype2 && \
    docker-php-ext-install gd

# Instala o SOAP
RUN apt-get -y update && \
    apt-get -y install libxml2-dev && \
    docker-php-ext-install soap

#Habilita a extensão zip
RUN docker-php-ext-install zip

RUN cd /usr/src && \
    tar -xvf php.tar.xz && \
    cp /usr/src/php-5.6.40/php.ini-development /usr/local/etc/php/php.ini

# Instala o Xdebug
RUN pecl install xdebug-2.5.5 && \
    docker-php-ext-enable xdebug
COPY xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini

# Set up the Oracle environment variables
ENV LD_LIBRARY_PATH /usr/lib/oracle/12.1/client64/lib/
ENV ORACLE_HOME /usr/lib/oracle/12.1/client64/lib/

# Install the OCI8 PHP extension
RUN echo 'instantclient,/usr/lib/oracle/12.1/client64/lib' | pecl install -f oci8-2.0.8
RUN echo "extension=oci8.so" > /usr/local/etc/php/conf.d/30-oci8.ini

#adiciona configurações customizadas
#OPY custom.ini /usr/local/etc/php/conf.d/custom.ini
RUN usermod -u 1000 www-data
RUN touch /var/log/php-errors.log \
&& chmod -Rf 777 /var/log/php-errors.log

#####################################
# WKHHTML:
#####################################
#COPY wkhtmltox_0.12.5-1.xenial_amd64.deb /tmp
#RUN dpkg -i /tmp/wkhtmltox_0.12.5-1.xenial_amd64.deb
#WORKDIR /var/www

CMD ["php-fpm"]

EXPOSE 9000