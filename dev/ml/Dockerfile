#######################################################################
# Usage :
# $ cd <path to root dir of python Machine Learning project>
# $ docker run -it --gpus all --network host --build-arg ssh_port=22222 --build-arg jupyterlab_port=28888 -v $(pwd):/home/ubuntu/work/project da1dock/devml
#######################################################################

FROM nvidia/cuda:10.1-devel
ARG root_password=root
ARG user=ubuntu
ARG user_password=ubuntu
ARG ssh_port=22
ARG jupyterlab_port=8888

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
ENV home /home/$user
WORKDIR $home
RUN sudo chmod a+rw . && \
    sudo add-apt-repository ppa:kelleyk/emacs && \
    sudo apt-get update && \
    sudo apt-get install -y --no-install-recommends \
      build-essential=12.4ubuntu1 \
      emacs26 \
      git=1:2.17.1-1ubuntu0.7 \
      less \
      libblas-dev=3.7.1-4ubuntu1 \
      openssh-server \
      unzip \
      vim \
      wget
RUN git clone git://github.com/daichi-yoshikawa/dotemacs .emacs.d

# Set cuda path.
USER $user
WORKDIR $home
ENV CUDA_ROOT /usr/local/cuda
ENV PATH $CUDA_ROOT/bin:$PATH
ENV LD_LIBRARY_PATH=$CUDA_ROOT/lib64:$LD_LIBRARY_PATH

# Set up python virtualenv.
USER $user
WORKDIR $home
ENV PYENV_ROOT $home/.pyenv
ENV PATH $PYENV_ROOT/shims:$PYENV_ROOT/bin:$PATH
RUN sudo apt-get install -y --no-install-recommends \
      gcc \
      make \
      openssl \
      libffi-dev \
      libssl-dev \
      libbz2-dev \
      libreadline-dev \
      libsqlite3-dev \
      zlib1g-dev
RUN git clone git://github.com/yyuu/pyenv.git .pyenv && \
    mkdir -p .pyenv/versions .pyenv/shims
RUN git clone git://github.com/yyuu/pyenv-virtualenv.git /home/$user/.pyenv/plugins/pyenv-virtualenv && \
    pyenv install 3.8.3 && \
    pyenv virtualenv 3.8.3 py383

# pip install python packages
WORKDIR $home
RUN sudo $home/.pyenv/bin/pyenv local py383 && \
    pip install --upgrade pip
RUN pip install numpy==1.19.1 pandas==1.1.0 matplotlib==3.3.0 seaborn==0.10.1 jupyterlab==2.2.2 plotly==4.9.0 bokeh==2.1.1
RUN pip install tensorflow==2.3.0 keras==2.4.3 keras-rl==0.4.2
RUN pip install torch==1.6.0+cu101 torchvision==0.7.0+cu101 -f https://download.pytorch.org/whl/torch_stable.html
RUN pip install nltk==3.5 spacy==2.3.2
RUN pip install xgboost==1.1.1 scikit-learn==0.23.1 scipy==1.4.1 cupy-cuda101==7.7.0 statsmodels==0.11.1

USER root
RUN echo '    StrictHostkeychecking no' >> /etc/ssh/ssh_config &&\
    echo "    Port $ssh_port" >> /etc/ssh/ssh_config

USER $user
#RUN sudo rm /etc/sudoers.d/$user
WORKDIR $home
EXPOSE $ssh_port $jupyterlab_port
ENV JUPYTERLAB_PORT=$jupyterlab_port
CMD sudo service ssh start && /bin/bash
#CMD /bin/bash
#EXPOSE $ssh_port $jupyterlab_port
#ENV JUPYTERLAB_PORT=$jupyterlab_port
#CMD service ssh start && su - ubuntu && /bin/bash && cd

#USER $user
#WORKDIR /home/$user
#RUN sudo rm /etc/sudoers.d/$user
#EXPOSE 22
#CMD /bin/bash
