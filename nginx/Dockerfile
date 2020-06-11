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

# Enable ssh login as root
WORKDIR /var/run/sshd
WORKDIR /etc/ssh
RUN sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' sshd_config && \
    sed -i 's/#PermitRootLogin yes/PermitRootLogin yes/' sshd_config
WORKDIR /etc/pam.d
RUN sed -i 's@session\s*required\s*pam_loginuid.so@session    optional    pam_loginuid#.so@g' sshd

WORKDIR /etc/nginx
RUN cp nginx.conf nginx.conf.bak && \
    cp conf.d/default.conf conf.d/default.conf.bak

VOLUME /var/log/nginx
VOLUME /etc/nginx/conf.d

WORKDIR $home
EXPOSE 22 80 8080
CMD service ssh start && service nginx start && /bin/bash
