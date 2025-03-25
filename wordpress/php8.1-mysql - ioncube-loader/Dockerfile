#Base Image
FROM ubuntu:jammy-20230816

# Install Apache

RUN apt-get update 
RUN apt-get -y install apache2


# Install Apache module
RUN a2enmod rewrite 

# ifconfig setup
#RUN apt-get -y install net-tools 

# add ping
#RUN apt-get -y install iputils-ping

# Create Apache spacific User
RUN useradd -m admin -u 1000 -G root


# Create user structure for Apache in home
RUN mkdir /home/admin/conf
RUN mkdir /home/admin/web
RUN mkdir /home/admin/tmp
RUN mkdir /home/admin/web/public_html


# Create user structure for Apache 
# for global configuration , logs and errors
RUN mkdir /etc/apache2/conf.d
RUN mkdir /var/log/apache2/domains/
RUN mkdir /home/admin/web/document_errors


# Copy preset Apache config files
COPY ./config/apache/apache2.conf /etc/apache2
COPY ./config/apache/ports.conf /etc/apache2
COPY ./config/apache/envvars /etc/apache2

COPY ./config/apache/conf.d/* /etc/apache2/conf.d


COPY ./config/web/main.apache2.conf /home/admin/conf
COPY ./config/document_errors/* /home/admin/web/document_errors


# Sample HTML file just for apache
#COPY ./config/web/index.html /home/admin/web/public_html

RUN apt-get update

# Install Git
RUN apt-get -y install git

# PHP 8.1 Begins Here
# Install dependencies
RUN apt-get -y install software-properties-common

# Add PHP Debian Repo
RUN add-apt-repository -y ppa:ondrej/php

# Refresh Indexes
RUN apt-get update

# Install PHP 8 on Ubuntu 18.04 LTS with predined timezone
RUN DEBIAN_FRONTEND=noninteractive TZ=Asia/Tehran apt-get -y install php8.1


# Install PHP 8 Extension
# disabled one : php8.1-mysql , 
# First install the more time consuming one
RUN apt -y install php8.1

# Then switch Back to PHP 8.1 
RUN update-alternatives --set php /usr/bin/php8.1

RUN apt-get update

# Add reamaining extensions 
RUN apt-get -y install php8.1-common php8.1-xml php8.1-xmlrpc php8.1-curl php8.1-gd php8.1-imagick php8.1-cli  php8.1-imap php8.1-mbstring php8.1-opcache php8.1-soap php8.1-zip php8.1-intl

# Install PHP 8 Extension
# mongo 
# RUN apt-get -y install php8.1-mongodb

# Install PHP 8 Extension
# MYSQL 
RUN apt-get install php8.1-mysql

# # Install Composer
RUN mkdir /composer
WORKDIR /composer

RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
RUN php composer-setup.php
RUN php -r "unlink('composer-setup.php');"
RUN mv composer.phar /usr/local/bin/composer

# RUN apt-get install -y phpmyadmin


# Install Laravel installer for global
#RUN composer global require laravel/installer
# #RUN echo 'export PATH="$HOME/.config/composer/vendor/bin:$PATH"' >> ~/.bashrc



#Switch Back to code
WORKDIR /home/admin/web/public_html

# Install Laravel in current directory
#  
# #RUN composer create-project laravel/laravel . 


# Final clean-up
#RUN rm -rf .??*
#RUN mv vendor ..
#RUN rm -rf *
# #RUN mv ../vendor .


# Give access to admin
#RUN chown -R admin:admin  /home/admin :Pointless after mapping all access would be admin


# Install Archive manager [7zip] for composer
RUN apt-get -y install zip unzip


# Create Apache Documentroot
RUN mkdir /home/admin/web/public_html/public

# Make tmp accessible for admin user
# Absolutely necessary for upload process
RUN chown -R admin:admin /home/admin/tmp

#Add Nano
RUN apt-get -y install nano



RUN apt-get -y install curl

USER admin

# #Add oh-my-bash because it is Cool
RUN bash -c "$(curl -fsSL https://raw.githubusercontent.com/ohmybash/oh-my-bash/master/tools/install.sh)"
COPY ./shell/.bashrc /home/admin
# RUN chown -R admin:admin /home/admin




## =========== Install Nodejs
USER root

# ioncube loader
RUN sh -c "$(curl -fsSL http://downloads3.ioncube.com/loader_downloads/ioncube_loaders_lin_x86-64.tar.gz  -o ioncube.tar.gz)"  \
    && mkdir -p ioncube \
    && tar -xf ioncube.tar.gz -C ioncube --strip-components=1 \
    && rm ioncube.tar.gz \
    && mv ioncube/ioncube_loader_lin_8.1.so /var/www/ioncube_loader_lin_8.1.so \
    && rm -r ioncube

COPY ./config/apache/php.ini /etc/php/8.1/apache2/php.ini

RUN apt-get update
RUN apt-get install -y ca-certificates curl gnupg
RUN mkdir -p /etc/apt/keyrings
RUN sh -c "$(curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key |  gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg)"
RUN echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_18.x nodistro main" | tee /etc/apt/sources.list.d/nodesource.list

RUN apt-get update
RUN apt-get install nodejs -y


# Lunch Server
RUN mkdir /lunch
COPY ./start.sh /lunch/


CMD [ "/lunch/start.sh" ]