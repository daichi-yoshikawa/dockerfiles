# When setting up host PC, launch this docker image with option
# of "--network=host".

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

USER root
RUN sudo echo '    StrictHostKeyChecking no' >> /etc/ssh/ssh_config
USER $user

WORKDIR /home/$user/playbooks
VOLUME /home/$user/playbooks

WORKDIR /home/$user
RUN echo '[defaults]' >> .ansible.cfg && \
    echo 'host_key_checking = False' >> .ansible.cfg

RUN sudo rm /etc/sudoers.d/$user
EXPOSE 22
CMD /bin/bash
