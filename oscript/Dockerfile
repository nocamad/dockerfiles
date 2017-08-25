FROM ubuntu:14.04

MAINTAINER NameMaintainer

ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update
RUN apt-get install -y -qq software-properties-common python-software-properties

RUN apt-get update \
    && apt-get install -y -qq --no-install-recommends \
	wget \
	curl \
	unzip \
	apt-transport-https \
	ca-certificates

RUN dpkg --add-architecture i386

RUN wget https://dl.winehq.org/wine-builds/Release.key \
    && apt-key add Release.key \
    && apt-add-repository 'https://dl.winehq.org/wine-builds/ubuntu/'

RUN apt-get update \
	&& apt-get install -y --no-install-recommends \
		wine \
	&& apt-get autoclean -y  \
	&& rm -rf /var/lib/apt/lists/*

# Add mono repository
RUN apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF && \
	echo "deb http://download.mono-project.com/repo/ubuntu trusty main" | tee /etc/apt/sources.list.d/mono-official.list

RUN apt-get update && apt-get install -y -qq \
# runtime dependencies
		mono-runtime \
		libmono-system-core4.0-cil \
		libmono-system4.0-cil \
		libmono-corlib4.0-cil \
		libmono-i18n4.0-all \
# deb-package tools
		dpkg\
		debconf\
		debhelper\
		lintian\
		md5deep\
		fakeroot \
		locales

RUN locale-gen --lang ru_RU.UTF-8
ENV LANG ru_RU.UTF-8

RUN add-apt-repository multiverse

RUN apt-get update
RUN apt-get install -y -qq unixodbc libgsf-1-114 imagemagick libglib2.0-dev libt1-5 t1utils libwebkit-dev libcanberra-gtk-module unzip xterm uuid fonty-rg fonts-ubuntu-font-family-console

# for onec work
RUN echo ttf-mscorefonts-installer msttcorefonts/accepted-mscorefonts-eula select true | debconf-set-selections | apt-get install -y -qq ttf-mscorefonts-installer

RUN apt-get install -y -qq xvfb
RUN apt-get install -y git-core

ENV PLT_VERSION 8.3.9-2170
ENV PLT_ARCH amd64

RUN mkdir /opt/dist

ADD ./dist/ /opt/dist/

RUN dpkg -i /opt/dist/onec/1c-enterprise83-common_${PLT_VERSION}_${PLT_ARCH}.deb \
            /opt/dist/onec/1c-enterprise83-server_${PLT_VERSION}_${PLT_ARCH}.deb \
            /opt/dist/onec/1c-enterprise83-client_${PLT_VERSION}_${PLT_ARCH}.deb

RUN rm /opt/dist/onec/*.deb

RUN dpkg -i /opt/dist/oscript/onescript-engine_1.0.16_all.deb

RUN apt-get autoclean -y && rm -rf /var/lib/apt/lists/*

RUN rm /opt/dist/oscript/*.deb

RUN mkdir /opt/data
RUN mkdir /opt/reps
RUN mkdir /opt/1C/v8.3/x86_64/conf && chmod -R 777 /opt/1C/v8.3/x86_64/conf
RUN mkdir -p /var/log/1c/logs && chmod -R 777 /var/log/1c

RUN chmod -R 777 /opt/1C/v8.3/x86_64

#VOLUME /opt/data
#VOLUME /opt/1C/v8.3/x86_64/conf/
#VOLUME /var/log/1c/logs

#CMD /bin/bash

# Install Java repository.
RUN apt-get update && apt-get -y upgrade && \
    apt-get -y install software-properties-common && \
    add-apt-repository ppa:webupd8team/java -y && apt-get update

# install oracle java 8 latest
RUN (echo oracle-java8-installer shared/accepted-oracle-license-v1-1 select true | /usr/bin/debconf-set-selections) && \
    apt-get install -y oracle-java8-installer oracle-java8-set-default

# install dependencies
RUN apt-get install -y wget git-core openssh-server

# SSH login fix. Otherwise user is kicked off after login
RUN sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd

# Add user user-slave to the image
RUN adduser --quiet user-slave

# Set password for the user-slave user 
RUN echo "user-slave:pass" | chpasswd

RUN usermod -g root user-slave

# config sshd
RUN mkdir -p /var/run/sshd

# Define commonly used JAVA_HOME variable
ENV JAVA_HOME /usr/lib/jvm/java-8-oracle
ENV PATH $JAVA_HOME/bin:$PATH

RUN mkdir -p /opt/slave
RUN chown user-slave:root /opt/slave

# Define working directory.
WORKDIR /opt/slave

# setup container timezone
ENV TZ=Europe/Moscow
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# Standard SSH port
EXPOSE 22

CMD ["/usr/sbin/sshd", "-D"]
