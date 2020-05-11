FROM nginx:1.18.0

ARG user=webapp
ARG user_password=webapp
ARG root_password=root

RUN apt-get update && \
    apt-get install -y --no-install-recommends \
            software-properties-common \
	    sudo && \
    useradd -ms /bin/bash -g sudo $user && \
    echo "root:$root_password" | chpasswd && \
    echo "$user:$user_password" | chpasswd && \
    echo "$user ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers.d/$user

USER $user
WORKDIR /home/$user
RUN sudo apt-get install -y --no-install-recommends \
            iputils-ping \
	    less \
	    net-tools \
            openssh-server \
            software-properties-common \
            vim

VOLUME app
VOLUME /var/log/nginx

USER root
RUN rm /etc/sudoers.d/$user && \
    mv /etc/nginx/conf.d/default.conf /etc/nginx/conf.d/default.conf.bak
COPY default.conf /etc/nginx/conf.d/default.conf

EXPOSE 22 80 443
CMD systemctl start ssh && systemctl start nginx && /bin/bash