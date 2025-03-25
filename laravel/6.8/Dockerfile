#Base Image
FROM ubuntu:bionic

## =====================================
# Install Apache
## =====================================
RUN apt-get update 
RUN apt-get -y install apache2



## =====================================
# Install Apache module
## =====================================
RUN a2enmod rewrite 


## =====================================
# Create Apache spacific User
## =====================================
RUN useradd -m admin -u 1000 -G root

## =====================================
# Create user structure for Apache in home
## =====================================

RUN mkdir /home/admin/conf
RUN mkdir /home/admin/web
RUN mkdir /home/admin/tmp
RUN mkdir /home/admin/web/public_html

## =====================================
# Create user structure for Apache 
# for global configuration , logs and errors
## =====================================

RUN mkdir /etc/apache2/conf.d
RUN mkdir /var/log/apache2/domains/
RUN mkdir /home/admin/web/document_errors

## =====================================
# Copy preset Apache config files
## =====================================
COPY ./config/apache/apache2.conf /etc/apache2
COPY ./config/apache/ports.conf /etc/apache2
COPY ./config/apache/envvars /etc/apache2

COPY ./config/apache/conf.d/* /etc/apache2/conf.d


COPY ./config/web/main.apache2.conf /home/admin/conf
COPY ./config/document_errors/* /home/admin/web/document_errors



## ========Install Git========
RUN apt-get -y install git


## =====================================
# PHP 7.2 Begins Here
# Install dependencies
## =====================================
RUN apt-get -y install software-properties-common

# Add PHP Debian Repo
RUN add-apt-repository -y ppa:ondrej/php

# Refresh Indexes
RUN apt-get update

## =======================================================
# Install PHP 8 on Ubuntu 18.04 LTS with predined timezone
## =======================================================
RUN DEBIAN_FRONTEND=noninteractive TZ=Asia/Tehran apt-get -y install php7.2

## ========php7.2========
RUN apt -y install php7.2

# Refresh Indexes
RUN apt-get update
 
## =====================================
# Add reamaining extensions 
## =====================================
RUN apt-get -y install php7.2-common \
    php7.2-xml \
    php7.2-xmlrpc\
    php7.2-curl\
    php7.2-gd \
    php7.2-imagick \
    php7.2-cli  \
    php7.2-imap \
    php7.2-mbstring \
    php7.2-opcache \
    php7.2-soap \
    php7.2-zip \
    php7.2-intl

## =====================================
# Install PHP 7.2 MongoDB Extension
## =====================================
# RUN apt-get -y install php7.2-mongodb

RUN apt-get -y install php-pear 

RUN apt-get -y install php7.2-dev

RUN pecl install mongodb-1.9.0


COPY ./config/apache/20-mongodb.ini /etc/php/7.2/apache2/conf.d/
COPY ./config/apache/20-mongodb.ini /etc/php/7.2/cli/conf.d/


## =====================================
# Install Composer
## =====================================
RUN mkdir /composer
WORKDIR /composer

RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
RUN php composer-setup.php
RUN php -r "unlink('composer-setup.php');"
RUN mv composer.phar /usr/local/bin/composer


## ========Work Directory=========+
WORKDIR /home/admin/web/public_html



## =====================================
# Install Archive manager [7zip] for composer
## =====================================
RUN apt-get -y install zip unzip

## =====================================
# Create Apache Documentroot
## =====================================
RUN mkdir /home/admin/web/public_html/public


## =====================================
## Make tmp accessible for admin user
## Absolutely necessary for upload process
## =====================================
RUN chown -R admin:admin /home/admin/tmp

## =====================================
## Add Nano
## =====================================
RUN apt-get -y install nano

RUN apt-get install curl

## ======Switch Admin User======
USER admin

## =====================================
## Add oh-my-bash because it is Cool
## =====================================
RUN bash -c "$(curl -fsSL https://raw.githubusercontent.com/ohmybash/oh-my-bash/master/tools/install.sh)"

COPY ./shell/.bashrc /home/admin/.bashrc

## =====================================
## Install Nodejs Version 14
## =====================================

## ======Switch root User====== 
USER root
RUN apt-get update

RUN curl -sL https://deb.nodesource.com/setup_14.x | bash -

RUN apt update

RUN apt-get install nodejs -y

# Lunch Server
RUN mkdir /lunch
COPY ./start.sh /lunch/


CMD [ "/lunch/start.sh" ]