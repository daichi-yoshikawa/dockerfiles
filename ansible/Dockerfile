FROM ubuntu:18.04
ARG user=ansible
ARG user_password=ansible
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
RUN sudo apt-add-repository -y --update ppa:ansible/ansible && \
    sudo apt-get install -y --no-install-recommends \
      ansible \
      git \
      iputils-ping \
      less \
      net-tools \
      openssh-client \
      openssh-server \
      vim

USER root
RUN rm /etc/sudoers.d/$user
ENV USER_=$user

EXPOSE 22
CMD service ssh start && su - ${USER_} && /bin/bash