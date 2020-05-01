# Usage

## Step 1
In the host machine, generate ssh key for ansible server as below.
```
ssh-keygen -t rsa -b 4096 -N "$(date)" -f <some-directory>/.ssh/id_rsa_ansible
```
<some-directory> is not necessarily home directory, especially in case of
that you don't like to share keys between the host machine and the docker container.

## Step 2
In the host machine, become root user and run as below.
```
sudo su
docker run -it ansible
```
If you need to put docker container in the same network as
the host machine's, you can do as below.
```
docker run --network=host -it ansible
```

## Step 3
In the launched docker container, you change user password and root password. You can use passwd to change user password.

## Step 4
In the launched docker container, you git clone and run ansible as you like.

## Optional
This container do NOT start sshd automatically on purpose. If you'd like to ssh login to the container, you need to start sshd in docker container by following command.
```
sudo service sshd start
```