FROM nginx:1.18.0

ARG root_password=root
ARG home=/root

RUN apt-get update && \
    apt-get install -y --no-install-recommends \
            apt-utils \
            software-properties-common \
            iputils-ping \
	    less \
	    net-tools \
            openssh-server \
            software-properties-common \
            vim && \
    echo "root:$root_password" | chpasswd

VOLUME /usr/share/nginx/html
VOLUME /var/log/nginx

WORKDIR /etc/nginx
RUN cp nginx.conf nginx.conf.bak && \
    cp conf.d/default.conf conf.d/default.conf.bak

WORKDIR $home

EXPOSE 22 80 443
CMD service ssh start && service nginx start && /bin/bash
