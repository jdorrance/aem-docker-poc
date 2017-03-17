# DOCKER-VERSION 1.7.0
FROM ubuntu:14.04
LABEL version="1.0"
LABEL description="AEM publish dispatcher.  Uses Apache and Google Pagespeed"
MAINTAINER dbenge

ENV DEBIAN_FRONTEND noninteractive
ENV DISPATCHER_TIMEZONE "US/Pacific"

RUN apt-get update && apt-get install -y \
    	dos2unix \
    	software-properties-common \
    	apache2 \
    	apache2-utils \
    	libssl1.0.0 \
    	libssl-dev \
    	&& rm -rf /var/lib/apt/lists/*	

# Install pagespeed and add dispatcher mod - Linux x86 64bit OpenSSL 1.0
ADD https://dl-ssl.google.com/dl/linux/direct/mod-pagespeed-stable_current_amd64.deb /aem/dispatcher/pagespeed/module/
ADD https://www.adobeaemcloud.com/content/companies/public/adobe/dispatcher/dispatcher/_jcr_content/top/download_8/file.res/dispatcher-apache2.4-linux-x86-64-4.2.1.tar.gz /aem/dispatcher/module/

# Configure timezone and locale
RUN echo $DISPATCHER_TIMEZONE > /etc/timezone && \
	dpkg-reconfigure -f noninteractive tzdata && \
	export LANGUAGE=en_US.UTF-8 && \
	export LANG=en_US.UTF-8 && \
	export LC_ALL=en_US.UTF-8 && \
	locale-gen en_US.UTF-8 && \
	DEBIAN_FRONTEND=noninteractive dpkg-reconfigure locales


WORKDIR /aem/dispatcher/pagespeed/module
RUN dpkg -i mod-pagespeed-*.deb
RUN apt-get -y -f install

# Install Dispatcher
WORKDIR /aem/dispatcher/module/
RUN tar -zxvf *.gz && \
	chown -R www-data:www-data *

WORKDIR /aem/dispatcher/
RUN mkdir logs
COPY resources/dispatcher.any dispatcher.any
RUN dos2unix dispatcher.any
WORKDIR /lib/x86_64-linux-gnu/
RUN ln -s libssl.so.1.0.0 libssl.so.10 && \
	ln -s libcrypto.so.1.0.0 libcrypto.so.10

#Copies required mods
WORKDIR /etc/apache2/mods-available/
COPY resources/mods-available/* ./
RUN dos2unix ./*
WORKDIR /etc/apache2/sites-available/
COPY resources/sites-available/* ./
RUN dos2unix ./*

#enable mods and site
RUN a2enmod rewrite dispatcher ssl pagespeed expires headers && \
	a2dissite 000-default && \
	a2ensite aemsite

EXPOSE 80 443

ENTRYPOINT ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]
#ENTRYPOINT ["/bin/sh"]

