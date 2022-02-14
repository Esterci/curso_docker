#FROM httpd:2.4.41
FROM php:5.6.40-apache

# Copy the source code into /var/www/html/ inside the image
COPY magnus /var/www/html/
COPY site.conf /var/www/html/
COPY certs/mycert.crt /etc/ssl/certs/
COPY private/mycert.key /etc/ssl/private/
COPY site.conf /etc/apache2/sites-available/000-default.conf

RUN apt-get update -yqq \
  && apt-get install -yqq --no-install-recommends \
    git \
    zip \
    unzip \
  && rm -rf /var/lib/apt/lists

# Enable PHP extensions
RUN docker-php-ext-install pdo_mysql mysqli

RUN a2enmod rewrite && a2enmod ssl && a2enmod socache_shmcb
RUN sed -i '/SSLCertificateFile.*snakeoil\.pem/c\SSLCertificateFile \/etc\/ssl\/certs\/mycert.crt' /etc/apache2/sites-available/default-ssl.conf && sed -i '/SSLCertificateKeyFile.*snakeoil\.key/cSSLCertificateKeyFile /etc/ssl/private/mycert.key\' /etc/apache2/sites-available/default-ssl.conf
RUN a2ensite default-ssl
RUN apt-get update && apt-get upgrade -y


# Install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/bin/ --filename=composer

# Add cake and composer command to system path
ENV PATH="${PATH}:/var/www/html/lib/Cake/Console"
ENV PATH="${PATH}:/var/www/html/app/Vendor/bin"

# COPY apache site.conf file

# Set default working directory
WORKDIR /app

# Create tmp directory and make it writable by the web server
RUN mkdir -p \
    tmp/cache/models \
    tmp/cache/persistent \
  && chown -R :www-data \
    tmp 
  #&& chmod -R 777 /

EXPOSE 80
EXPOSE 443

#RUN docker-php-ext-install mysqli && docker-php-ext-enable mysqli
#RUN apt-get update && apt-get upgrade -y

#WORKDIR /src

#COPY magnus/* /var/www/html/