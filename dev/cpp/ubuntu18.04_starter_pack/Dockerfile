FROM ubuntu:18.04
ARG user=ubuntu
ARG user_password=ubuntu
ARG root_password=root
ARG eigen_version=3.3.7
ARG yamlcpp_version=0.6.3
ARG boost_version=1_73_0

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
RUN sudo chmod a+rw . && \
    sudo add-apt-repository ppa:kelleyk/emacs && \
    sudo apt-get update && \
    sudo apt-get install -y --no-install-recommends \
      build-essential=12.4ubuntu1 \
      cmake=3.10.2-1ubuntu2.18.04.1 \
      emacs26 \
      git=1:2.17.1-1ubuntu0.7 \
      libblas-dev=3.7.1-4ubuntu1 \
      openssh-server \
      unzip \
      vim \
      wget
RUN git clone https://github.com/daichi-yoshikawa/dotemacs .emacs.d

USER $user
WORKDIR /home/$user/local/eigen-$eigen_version
RUN sudo chmod a+rw .
WORKDIR /home/$user/tmp
RUN sudo chmod a+rw . && \
    wget -q https://gitlab.com/libeigen/eigen/-/archive/$eigen_version/eigen-$eigen_version.tar.gz && \
    tar zxf eigen-$eigen_version.tar.gz && \
    rm eigen-$eigen_version.tar.gz && \
    mkdir eigen-$eigen_version/build && \
    cd eigen-$eigen_version/build && \
    cmake -DCMAKE_INSTALL_PREFIX=/home/$user/local/eigen-$eigen_version -DCMAKE_BUILD_TYPE=Release .. && \
    make -j && \
    make install

WORKDIR /home/$user/local/yaml-cpp-$yamlcpp_version
RUN sudo chmod a+rw .
WORKDIR /home/$user/tmp
RUN sudo chmod a+rw . && \
    wget -q https://github.com/jbeder/yaml-cpp/archive/yaml-cpp-$yamlcpp_version.zip && \
    unzip yaml-cpp-$yamlcpp_version.zip && \
    rm yaml-cpp-$yamlcpp_version.zip && \
    mkdir yaml-cpp-yaml-cpp-$yamlcpp_version/build && \
    cd yaml-cpp-yaml-cpp-$yamlcpp_version/build && \
    cmake -DCMAKE_INSTALL_PREFIX=/home/$user/local/yaml-cpp-$yamlcpp_version -DCMAKE_BUILD_TYPE=Release .. && \
    make -j && \
    make install

WORKDIR /home/$user/local/boost_$boost_version
RUN sudo chmod a+rw .
WORKDIR /home/$user/tmp
RUN sudo chmod a+rw . && \
    wget -q https://dl.bintray.com/boostorg/release/1.73.0/source/boost_$boost_version.tar.gz && \
    tar zxf boost_$boost_version.tar.gz && \
    rm boost_$boost_version.tar.gz && \
    cd boost_$boost_version && \
    ./bootstrap.sh && \
    ./b2 install --prefix=/home/ubuntu/local/boost_$boost_version

WORKDIR /home/$user/work
RUN sudo chmod a+rw .

USER root
RUN echo '    StrictHostkeychecking no' >> /etc/ssh/ssh_config

USER $user
WORKDIR /home/$user
RUN sudo rm /etc/sudoers.d/$user
EXPOSE 22
CMD /bin/bash
