#---Ubuntu 22.04 LTS -----
FROM ubuntu:jammy-20230816

#-----------------------------------
# # Install Apache
#-----------------------------------
RUN apt-get update 

RUN apt-get -y install apache2

#-----------------------------------
# # Install Apache module
#-----------------------------------
RUN a2enmod rewrite 

#-----------------------------------
# # Create Apache spacific User
#-----------------------------------
RUN useradd -m admin -u 1000 -G root

#-------------------------------------------
# # Create user structure for Apache in home
#-------------------------------------------
RUN mkdir /home/admin/conf
RUN mkdir /home/admin/web
RUN mkdir /home/admin/tmp
RUN mkdir /home/admin/web/public_html

#---------------------------------------------
# # Create user structure for Apache 
#   for global configuration , logs and errors
#---------------------------------------------
RUN mkdir /etc/apache2/conf.d
RUN mkdir /var/log/apache2/domains/
RUN mkdir /home/admin/web/document_errors

#-------------------------------------
# # Copy preset Apache config files
# -------------------------------------
COPY ./config/apache/apache2.conf /etc/apache2
COPY ./config/apache/ports.conf /etc/apache2
COPY ./config/apache/envvars /etc/apache2

COPY ./config/apache/conf.d/* /etc/apache2/conf.d

COPY ./config/web/main.apache2.conf /home/admin/conf
COPY ./config/document_errors/* /home/admin/web/document_errors


#--- Refresh Indexes---
RUN apt-get update


#---- Install Git----
RUN apt-get -y install git


#-------------------------------------
# PHP 8.3 Begins Here
# Install dependencies
#-------------------------------------
RUN apt-get -y install software-properties-common

#---- Add PHP Debian Repo----
RUN add-apt-repository -y ppa:ondrej/php

#---- Refresh Indexes-----
RUN apt-get update

#---------------------------------------------------------
# Install PHP 8 on Ubuntu 18.34 LTS with predined timezone
#---------------------------------------------------------
RUN DEBIAN_FRONTEND=noninteractive TZ=Asia/Tehran apt-get -y install php8.3

#-------------------------------------------
# Install PHP 8 Extension
# dis
# abled one : php8.3-mysql , 
# First install the more time consuming one
#-------------------------------------------
RUN apt -y install php8.3

#--------- Then switch Back to PHP 8.3 ----------
RUN update-alternatives --set php /usr/bin/php8.3


#-------------------------------------------
# Set Variable php.ini
#-------------------------------------------
ENV MAX_EXECUTION_TIME 60
ENV MAX_INPUT_TIME 120
ENV MEMORY_LIMIT 256M
ENV POST_MAX_SIZE 16M
ENV UPOAD_MAX_FILEZIE 8M
ENV MAX_FILE_UPLOADS 50


#-------------------------------------------
# Set Variable php.ini
#-------------------------------------------

COPY ./config/apache/php.ini /etc/php/8.3/apache2/php.ini

#---- Refresh Index----
RUN apt-get update

#------- Add reamaining extensions --------
RUN apt-get -y install php8.3-common \ 
    php8.3-xml \
    php8.3-xmlrpc \
    php8.3-curl \
    php8.3-gd \
    php8.3-imagick \
    php8.3-cli  \
    php8.3-imap \
    php8.3-mbstring \
    php8.3-opcache \
    php8.3-soap \
    php8.3-zip \
    php8.3-intl


#-------------------------------------------
# Install PHP 8 Extension
# MYSQL 
#-------------------------------------------
RUN apt-get -y install php8.3-mongodb


# -----Restart Apache2------
# RUN service apache2 stop

# -------------------------------------------
# # Install Composer
# -------------------------------------------
RUN mkdir /composer
WORKDIR /composer

RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
RUN php composer-setup.php
RUN php -r "unlink('composer-setup.php');"
RUN mv composer.phar /usr/local/bin/composer

# ----- Switch Back to code -------
WORKDIR /home/admin/web/public_html


#-----------------------------------------------
# # Install Archive manager [7zip] for composer
#-----------------------------------------------
RUN apt-get -y install zip unzip

# ----- Create Apache Documentroot ---------
RUN mkdir /home/admin/web/public_html/public

#------------------------------------------
# Make tmp accessible for admin user
# Absolutely necessary for upload process
#------------------------------------------
RUN chown -R admin:admin /home/admin/tmp

# ----Add Nano ----
RUN apt-get -y install nano


# ---- Curl ---------------
RUN apt-get -y install curl


USER admin
#-------------------------------------
# # Add oh-my-bash because it is Cool
#-------------------------------------
RUN bash -c "$(curl -fsSL https://raw.githubusercontent.com/ohmybash/oh-my-bash/master/tools/install.sh)"

COPY ./shell/.bashrc /home/admin/.bashrc

#-------------------------------------
# Install Nodejs
#-------------------------------------
USER root



RUN apt-get update
RUN curl -fsSL https://fnm.vercel.app/install | bash

RUN echo 'export PATH=$PATH:$HOME/.fnm' >> ~/.bashrc && \
    echo 'eval "$(fnm env)"' >> ~/.bashrc

RUN . ~/.bashrc && fnm use --install-if-missing 22

RUN apt-get update
# RUN apt-get install nodejs -y


# --- Lunch Server
RUN mkdir /lunch
COPY ./start.sh /lunch/

RUN chown -R admin:admin /lunch/start.sh
RUN chmod -R 755 /lunch/start.sh


CMD [ "/lunch/start.sh" ]