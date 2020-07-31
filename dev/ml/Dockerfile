#######################################################################
# Usage :
# $ cd <path to root dir of python Machine Learning project>
# $ docker run -it --gpus all -p <ssh port in host>:22 -p <jupyter lab port in host>:8888 -v $(pwd):/home/ubuntu/work/project da1dock/devml
# Eg.
# $ docker run -it --gpus all -p 22222:22 -p 28888:8888 -v $(pwd):/home/ubuntu/work da1dock/dev-ml
# With the above example,
# you can ssh login docker container by "ssh -p 22222 ubuntu@127.0.0.1",
# and use jupyter lab with URL of "localhost:28888" in browser.
# When doing ssh login, execute "$ source env.sh" to setup environmental
# variables properly before doing anything.
#######################################################################

FROM nvidia/cuda:10.1-cudnn7-devel-ubuntu18.04
ARG root_password=root
ARG user=ubuntu
ARG user_password=ubuntu

RUN apt-get update && \
    apt-get install -y --no-install-recommends \
      software-properties-common \
      sudo && \
    useradd -ms /bin/bash -g sudo $user && \
    mkhomedir_helper $user && \
    echo "root:$root_password" | chpasswd && \
    echo "$user:$user_password" | chpasswd && \
    echo "$user ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers.d/$user

USER $user
ENV HOME /home/$user
WORKDIR $HOME
RUN sudo chmod a+rw . && \
    sudo add-apt-repository ppa:kelleyk/emacs && \
    sudo apt-get update && \
    sudo apt-get install -y --no-install-recommends \
      apt-utils \
      build-essential \
      emacs26 \
      git \
      less \
      libblas-dev \
      lsof \
      openssh-server \
      unzip \
      vim \
      wget
RUN git clone git://github.com/daichi-yoshikawa/dotemacs .emacs.d

# Set cuda path.
USER $user
WORKDIR $HOME
ENV CUDA_ROOT /usr/local/cuda
ENV PATH $CUDA_ROOT/bin:$PATH
ENV LD_LIBRARY_PATH=$CUDA_ROOT/lib64:$LD_LIBRARY_PATH

# Set up python virtualenv.
USER $user
WORKDIR $HOME
ENV PYENV_ROOT $HOME/.pyenv
ENV PATH $PYENV_ROOT/shims:$PYENV_ROOT/bin:$PATH
RUN sudo apt-get install -y --no-install-recommends \
      gcc \
      libbz2-dev \
      libffi-dev \
      liblzma-dev \
      libreadline-dev \
      libsqlite3-dev \
      libssl-dev \
      make \
      openssl \
      zlib1g-dev
RUN git clone git://github.com/yyuu/pyenv.git .pyenv && \
    mkdir -p .pyenv/versions .pyenv/shims
RUN git clone git://github.com/yyuu/pyenv-virtualenv.git $HOME/.pyenv/plugins/pyenv-virtualenv && \
    pyenv install 3.8.3 && \
    pyenv virtualenv 3.8.3 py383

# pip install python packages
WORKDIR $HOME/work
RUN sudo $HOME/.pyenv/bin/pyenv local py383 && \
    pip install --upgrade pip
RUN pip install numpy==1.19.1 pandas==1.1.0 matplotlib==3.3.0 seaborn==0.10.1 jupyterlab==2.2.2 plotly==4.9.0 bokeh==2.1.1
RUN pip install tensorflow==2.3.0 keras==2.4.3 keras-rl==0.4.2
RUN pip install torch==1.6.0+cu101 torchvision==0.7.0+cu101 -f https://download.pytorch.org/whl/torch_stable.html
RUN pip install nltk==3.5 spacy==2.3.2
RUN pip install xgboost==1.1.1 scikit-learn==0.23.1 scipy==1.4.1 cupy-cuda101==7.7.0 statsmodels==0.11.1

USER root
RUN echo '    StrictHostkeychecking no' >> /etc/ssh/ssh_config

USER $user
WORKDIR $HOME
ENV USER $user
RUN /bin/bash -c "declare -p > .env.sh" && \
    echo 'source $HOME/.env.sh' >> $HOME/.bashrc && \
    echo 'export TERM=xterm-256color' >> $HOME/.bashrc
EXPOSE 22 8888
WORKDIR $HOME/work
CMD sudo service ssh start && sudo rm /etc/sudoers.d/$USER && /bin/bash && jupyter lab --allow-root --ip=0.0.0.0 --no-browser
