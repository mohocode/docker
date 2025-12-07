FROM ubuntu:jammy-20230816
RUN apt-get update 
RUN apt-get -y install apache2
RUN a2enmod rewrite 

#--- Refresh Indexes---
    RUN apt-get update


#---- Install Git----
RUN apt-get -y install git

RUN useradd -m admin -u 1000 -G root

#============================ 
# Docker Creat Directories
#============================
RUN mkdir /home/admin/conf
RUN mkdir /home/admin/web
RUN mkdir /home/admin/tmp
RUN mkdir /home/admin/web/public_html
RUN mkdir /etc/apache2/conf.d
RUN mkdir /var/log/apache2/domains/
RUN mkdir /home/admin/web/document_errors

#============================ 
# Docker Aapache Config
#============================
COPY ./config/apache/apache2.conf /etc/apache2
COPY ./config/apache/ports.conf /etc/apache2
COPY ./config/apache/envvars /etc/apache2
COPY ./config/apache/conf.d/* /etc/apache2/conf.d
COPY ./config/web/main.apache2.conf /home/admin/conf
COPY ./config/document_errors/* /home/admin/web/document_errors


#==========================
# Set Variable php.ini
#==========================
    ENV MAX_EXECUTION_TIME=60
    ENV MAX_INPUT_TIME=120
    ENV MEMORY_LIMIT=256M
    ENV POST_MAX_SIZE=16M
    ENV UPOAD_MAX_FILEZIE=8M
    ENV MAX_FILE_UPLOADS=50

#============================ 
# Docker php.ini
#============================
COPY ./config/apache/php.ini /etc/php/8.1/apache2/php.ini


RUN apt-get update
RUN apt-get upgrade -y
RUN apt-get install software-properties-common -y
RUN add-apt-repository ppa:ondrej/php -y
RUN apt-get update
RUN DEBIAN_FRONTEND=noninteractive TZ=Asia/Tehran apt-get -y install php8.1
RUN apt -y install php8.1
RUN update-alternatives --set php /usr/bin/php8.1
RUN apt-get -y install php8.1-common \ 
                      php8.1-xml \
                      php8.1-xmlrpc \
                      php8.1-curl \
                      php8.1-gd \
                      php8.1-imagick \
                      php8.1-cli \
                      php8.1-imap \
                      php8.1-mbstring \
                      php8.1-opcache \
                      php8.1-soap \
                      php8.1-zip \
                      php8.1-intl
RUN apt-get install php8.1-mysql

#============================ 
# Install Composer
#============================
    RUN mkdir /composer
    WORKDIR /composer
    
    RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
    RUN php composer-setup.php
    RUN php -r "unlink('composer-setup.php');"
    RUN mv composer.phar /usr/local/bin/composer
    
    # ----- Switch Back to code -------
    WORKDIR /home/admin/web/public_html


#============================ 
# Install IonCube
#============================
RUN apt-get update && apt-get install -y curl bash 

RUN bash -c "$(curl -fsSL http://downloads3.ioncube.com/loader_downloads/ioncube_loaders_lin_x86-64.tar.gz  -o ioncube.tar.gz)"  \
    && mkdir -p ioncube \
    && tar -xf ioncube.tar.gz -C ioncube --strip-components=1 \
    && rm ioncube.tar.gz \
    && mv ioncube/ioncube_loader_lin_8.1.so /var/www/ioncube_loader_lin_8.1.so \
    && rm -r ioncube


#============================ 
# Install Nodejs
#============================

RUN bash -c "curl -fsSL https://deb.nodesource.com/setup_22.x | bash "
RUN apt install -y nodejs

#============================ 
# Install Oh My Bash
#============================
USER admin
RUN bash -c "$(curl -fsSL https://raw.githubusercontent.com/ohmybash/oh-my-bash/master/tools/install.sh)"
COPY ./shell/.bashrc /home/admin/.bashrc


USER root
# --- Lunch Server
RUN mkdir /lunch
COPY ./start.sh /lunch/
RUN chown -R admin:admin /home/admin/tmp
RUN chown -R admin:admin /home/admin/web
RUN chown -R admin:admin /lunch/start.sh
RUN chmod -R 755 /lunch/start.sh


CMD [ "/lunch/start.sh" ]


