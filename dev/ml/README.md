# dev-ml

This docker image, dev-ml is on top of the NVIDIA's docker image, nvidia/cuda:10.1-cudnn7-devel-ubuntu18.04.

It is not for application, but to provide development environment for machine learning project. With this dev env, you can

* work on python project which requires CUDA 10.1 and CuDNN 7
* use python deep learning frameworks(tensorflow, keras, torch) out of box
* use other machine learning packages(scikit-learn, xgboost, cupy, etc.) out of box
* use jupyter lab with a browser launched on your host machine, since jupyter lab server will run on container
* ssh login to container and edit with emacs or vim out of box

From the security purpose, do not run this on the server, I strongly recommend that you just run on your local machine.

## Requisites

All must be done in your host machine which have GPU avilable for CUDA 10.1.
* Install docker-ce (>= 19.03)
* Install nvidia driver
* Install [nvidia-container-toolkit](https://github.com/NVIDIA/nvidia-docker)

Note: you don't need to install CUDA, CuDNN, and python in your host machine.

## Usage

Our goal is to run python script from docker container, especially the script which requires GPU resources and utilizes machine learning framework, CUDA and CuDNN.

Assume that you create python project dir as /home/user/work/my_proj in your host machine,
and implemented test.py as follow in the dir. This is very simple implementation of neural network for MNIST dataset.

```
from keras import models, layers
from keras.utils import to_categorical
from keras.datasets import mnist

(train_images, train_labels), (test_images, test_labels) = mnist.load_data()

network = models.Sequential()
network.add(layers.Dense(512, activation='relu', input_shape(28*28,)))
network.add(layers.Dense(10, activation='softmax'))

network.compile(optimizer='rmsprop', loss='categorical_crossentropy', metrics=['accuracy'])

train_images = train_images.reshape((60000, 28*28))
train_images = train_images.astype('float32') / 255
test_images = test_images.reshape((10000, 28*28))
test_images = test_images.astype('float32') / 255

train_labels = to_categorical(train_labels)
test_labels = to_categorical(test_labels)

network.fit(train_images, train_labels, epochs=5, batch_size=128)
test_loss, test_acc = network.evaluate(test_images, test_labels)
print('test_acc:', test_acc)
```

All you need to do is go to the directory and run the following commands.
```
$ docker run -it --gpus all -v /home/user/work/my_proj:/home/ubuntu/work/project da1dock/dev-ml
```

And then you can work on the project and run script in multiple ways.
### Option 1. Use the same terminal where you launched the docker container
After executed the above command, you seamlessly start the interaction with shell in the container. So all you need to do is
```
$ cd /home/ubuntu/work
$ python test.py
```

### Option 2. SSH login to the container with other terminal(s) in host machine, and run script there
```
$ ssh ubuntu@127.0.0.1
<answer password, default: ubuntu>
$ cd /home/ubuntu/work
$ python test.py
```

### Option 3. Use jupyter lab in a browser launched in your host machine
With running docker container, launch a browser in your host machine, type
"localhost:8888" in URL bar, and then you'll see jupypter lab.

### Change port(s) for ssh and/or jupyter lab
You can specify port(s) you'd like to use on your host machine side. For example, if you'd like to use port 22222 for ssh and 28888 for jupyter lab instead of default port 22 and 8888 respectively, add options as follow when running docker container.
```
$ docker run -it --gpus all -p 22222:22 -p 28888:8888 -v /home/user/work/proj:/home/ubuntu/work/project da1dock/dev-ml
```
