#Base Image

FROM ubuntu:latest

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


# Install Git
RUN apt-get -y install git

# PHP 8.2 Begins Here
# Install dependencies
RUN apt-get -y install software-properties-common

# Add PHP Debian Repo
RUN add-apt-repository -y ppa:ondrej/php

# Refresh Indexes
RUN apt-get update

# Install PHP 8 on Ubuntu 18.04 LTS with predined timezone
RUN DEBIAN_FRONTEND=noninteractive TZ=Asia/Tehran apt-get -y install php8.2


# Install PHP 8 Extension
# disabled one : php8.2-mysql , 
# First install the more time consuming one
RUN apt-get -y install php8.2-dev

# Then switch Back to PHP 8.2 
RUN update-alternatives --set php /usr/bin/php8.2

# Add reamaining extensions 
RUN apt-get -y install php8.2-common php8.2-xml php8.2-xmlrpc php8.2-curl php8.2-gd php8.2-imagick php8.2-cli  php8.2-imap php8.2-mbstring php8.2-opcache php8.2-soap php8.2-zip php8.2-intl

# Install PHP 8 Extension
# mongo 
# RUN apt-get -y install php8.2-mongodb

# Install PHP 8 Extension
# MYSQL 
RUN apt-get install php8.2-mysql

# Install Composer
RUN mkdir /composer
WORKDIR /composer

RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
RUN php composer-setup.php
RUN php -r "unlink('composer-setup.php');"
RUN mv composer.phar /usr/local/bin/composer


# RUN apt-get install -y phpmyadmin


# Install Laravel installer for global
#RUN composer global require laravel/installer
#RUN echo 'export PATH="$HOME/.config/composer/vendor/bin:$PATH"' >> ~/.bashrc



#Switch Back to code
WORKDIR /home/admin/web/public_html

# Install Laravel in current directory
#  
#RUN composer create-project laravel/laravel . 


# Final clean-up
#RUN rm -rf .??*
#RUN mv vendor ..
#RUN rm -rf *
#RUN mv ../vendor .


# Give access to admin
#RUN chown -R admin:admin  /home/admin :Pointless after mapping all access would be admin

#TODO : Make zsh and oh my zsh for admin user with a nice theme

# Install Archive manager [7zip] for composer
RUN apt-get -y install zip unzip


# Create Apache Documentroot
RUN mkdir /home/admin/web/public_html/public

# Make tmp accessible for admin user
# Absolutely necessary for upload process
RUN chown -R admin:admin /home/admin/tmp

#Add Nano
RUN apt-get -y install nano


#Add zsh because it is Cool
RUN apt-get -y install zsh
RUN sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" "" --unattended

#Make zsh ready for admin
#RUN hostname arash-lara
#COPY ./shell/.zshrc /home/admin
RUN cp -r /root/.zshrc /home/admin
RUN cp -r /root/.oh-my-zsh /home/admin
RUN chown -R admin:admin /home/admin

#RUN su admin && /bin/bash -c "ln -s $HOME/.zshrc   /root/.zshrc"




# Lunch Server
RUN mkdir /lunch
COPY ./start.sh /lunch/


CMD [ "/lunch/start.sh" ]