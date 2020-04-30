# When setting up host PC, launch this docker image with option
# of "--network=host".

FROM ubuntu:18.04
ARG user=ansible
ARG user_password=ansible
ARG root_password=root
ARG playbook_url=https://github.com/daichi-yoshikawa/ansible_playbooks

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

WORKDIR /home/$user
RUN git clone $playbook_url

USER root
RUN rm /etc/sudoers.d/$user
ENV USER_=$user

EXPOSE 22
CMD service ssh start && su - ${USER_} && /bin/bash