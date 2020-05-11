FROM nginx:1.18.0

ARG user=webapp
ARG user_password=webapp
ARG root_password=root

RUN apt-get update && \
    apt-get install -y --no-install-recommends \
            iputils-ping \
	    less \
	    net-tools \
            openssh-server \
            software-properties-common \
            vim

WORKDIR /etc/nginx
RUN mv nginx.conf nginx.conf.bak && \
    mv conf.d/default.conf conf.d/default.conf.bak

COPY nginx.conf nginx.conf
COPY events_10_basic.conf conf.d/events_10_basic.conf
COPY http_10_basic.conf conf.d/http_10_basic.conf

WORKDIR /root/app/html
VOLUME /root/app/html

WORKDIR /root
VOLUME /var/log/nginx

EXPOSE 22 80 443
CMD service ssh start && service nginx start && /bin/bash