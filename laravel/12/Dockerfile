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
# PHP 8.2 Begins Here
# Install dependencies
#-------------------------------------
RUN apt-get -y install software-properties-common

#---- Add PHP Debian Repo----
RUN add-apt-repository -y ppa:ondrej/php

#---- Refresh Indexes-----
RUN apt-get update

#---------------------------------------------------------
# Install PHP 8 on Ubuntu 18.24 LTS with predined timezone
#---------------------------------------------------------
RUN DEBIAN_FRONTEND=noninteractive TZ=Asia/Tehran apt-get -y install php8.2

#-------------------------------------------
# Install PHP 8 Extension
# dis
# abled one : php8.2-mysql , 
# First install the more time consuming one
#-------------------------------------------
RUN apt -y install php8.2

#--------- Then switch Back to PHP 8.2 ----------
RUN update-alternatives --set php /usr/bin/php8.2


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

COPY ./config/apache/php.ini /etc/php/8.2/apache2/php.ini

#---- Refresh Index----
RUN apt-get update

#------- Add reamaining extensions --------
RUN apt-get -y install php8.2-common \ 
    php8.2-xml \
    php8.2-xmlrpc \
    php8.2-curl \
    php8.2-gd \
    php8.2-imagick \
    php8.2-cli  \
    php8.2-imap \
    php8.2-mbstring \
    php8.2-opcache \
    php8.2-soap \
    php8.2-zip \
    php8.2-intl


#-------------------------------------------
# Install PHP 8 Extension
# MYSQL 
#-------------------------------------------
RUN apt-get -y install php8.2-mongodb
RUN apt-get update && apt-get install -y \
    php8.2-mysql \
    php8.2-pgsql



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
RUN chown -R admin:admin /home/admin/web
RUN chown -R admin:admin /home/admin/web/public_html
RUN chown -R admin:admin /home/admin/conf

# ----Add Nano ----
RUN apt-get -y install nano


# ---- Curl ---------------
RUN apt-get update && apt-get install -y curl bash    

USER admin
#-------------------------------------
# # Add oh-my-bash because it is Cool
#-------------------------------------
RUN bash -c "$(curl -fsSL https://raw.githubusercontent.com/ohmybash/oh-my-bash/master/tools/install.sh)"

COPY ./shell/.bashrc /home/admin/.bashrc

RUN composer global require laravel/installer


USER root


RUN apt-get update

RUN mkdir -p /usr/local/nvm
# Install NVM in a global location (/usr/local/nvm)
ENV NVM_DIR="/usr/local/nvm"
ENV PATH="$NVM_DIR/versions/node/v22.14.0/bin:$PATH"

RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash

# Load NVM & Install Node.js 22 (as root)
RUN bash -c "source $NVM_DIR/nvm.sh && nvm install 22 && nvm alias default 22"

# Make nvm available to all users (root & admin)
RUN echo 'export NVM_DIR="/usr/local/nvm"' | tee -a /etc/profile.d/nvm.sh /root/.bashrc /home/admin/.bashrc && \
echo '[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"' | tee -a /etc/profile.d/nvm.sh /root/.bashrc /home/admin/.bashrc && \
echo 'export PATH="$NVM_DIR/versions/node/v22.14.0/bin:$PATH"' | tee -a /etc/profile.d/nvm.sh /root/.bashrc /home/admin/.bashrc

# Change ownership so admin can use nvm
RUN chown -R admin:admin /usr/local/nvm
RUN chown -R admin:admin /home/admin/.bashrc

RUN composer global require laravel/installer
RUN echo 'export PATH="$HOME/.config/composer/vendor/bin:$PATH"' >> $HOME/.bashrc \
          source $HOME/.bashrc

USER admin
RUN echo 'export PATH="$HOME/.config/composer/vendor/bin:$PATH"' >> $HOME/.bashrc \
          source $HOME/.bashrc

USER root
# --- Lunch Server
RUN mkdir /lunch
COPY ./start.sh /lunch/

RUN chown -R admin:admin /lunch/start.sh
RUN chmod -R 755 /lunch/start.sh


CMD [ "/lunch/start.sh" ]