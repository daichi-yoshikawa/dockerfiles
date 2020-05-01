# When setting up host PC, launch this docker image with option
# of "--network=host".

FROM ubuntu:18.04
ARG user=ansible
ARG user_password=ansible
ARG root_password=root
ARG ssh_pub_key=.ssh/id_rsa_ansible.pub
ARG ssh_pri_key=.ssh/id_rsa_ansible

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

RUN mkdir .ssh && chmod 700 .ssh
COPY $ssh_pub_key .ssh/id_rsa_ansible.pub
COPY $ssh_pri_key .ssh/id_rsa_ansible

RUN sudo rm /etc/sudoers.d/$user
EXPOSE 22
CMD /bin/bash