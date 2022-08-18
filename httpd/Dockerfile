FROM httpd:alpine


RUN echo "Include /usr/local/apache2/conf/docker/httpd.conf" >> /usr/local/apache2/conf/httpd.conf
RUN apk add python3 python3-dev py3-pip build-base libressl-dev musl-dev libffi-dev rust cargo
RUN pip3 install pip --upgrade
RUN pip3 install certbot-apache
RUN mkdir /etc/letsencrypt
