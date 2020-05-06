# Usage

## Step 1
In the host machine, generate ssh keys for ansible server as below. The generated keys are shared by this docker container via mounted volume.
```
ssh-keygen -t rsa -b 4096 -f <some-directory>/.ssh/id_rsa_ansible
```
For example,
```
ssh-keygen -t rsa -b 4096 -f /home/user_on_host/tmp/.ssh/id_rsa_ansible
```
<some-directory> is not necessarily home directory, especially in case of
that you don't like to share keys between the host machine and the docker container.

## Step 2
In the host machine, become root user and run as below.
```
sudo su
docker run -v <full-path-to-ssh-dir>:/home/<username of docker>/.ssh -it ansible
```
For example, the above will be like below(Default username is "ansible").
```
docker run -v /home/user_on_host/tmp/.ssh:/home/ansible/.ssh -it ansible
```

If you need to put docker container in the same network as
the host machine's, you can do as below.
```
docker run -v <full-path-to-ssh-dir>:/home/username of docker>/.ssh --network=host -it ansible
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

# Setting up no-password ssh
## Step 1
Copy public key of ansible server to the host machine. In ansible server, do below.
```
scp -P <port you use for ssh> <path-to-.ssh>/<public key name> <username of host>@<ip address of host>:<path-to-.ssh>/<public key name>
```
Example)
```
scp -P 22 .ssh/id_rsa_ansible.pub user_on_host@10.1.0.10:/home/user_on_host/.ssh/id_rsa_ansible.pub
```
Note: Be careful not to "-p" rather than "-P". Although ssh requires lowercase to specify port, but scp does uppercase.

## Step 2
Register the copied public key to authorized_keys. In the host machine,
```
cd <path-to-.ssh>
cat <public key name> >> authorized_keys
```
Example)
```
cd ~/.ssh
cat id_rsa_ansible.pub >> authorized_keys
```

Change user access just in case.
```
chmod 700 <path-to-.ssh>
chmod 600 <path-to-authorized_keys>
```
Example)
```
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

## Step 3
Edit sshd config. In the host machine, edit /etc/ssh/sshd_config.
```
sudo sed -i -e "s/#PubkeyAuthentication/PubkeyAuthentication/g" /etc/ssh/sshd_config
```
The above is just enabling PubkeyAuthentication by removing hash(#) symbol to comment out a line "PubkeyAuthentication yes". If "sed" command is unfamiliar and not comfortable to use, of course you can edit the file with your preferable editor and delete the hash manually.

## Step 4
Restart sshd in the host machine to reflect the update of sshd_config.
```
sudo service sshd restart
```

## Step 5
Test ssh login from ansible to the host machine. In ansible server,
```
ssh -p <port you use for ssh> -i <path-to-.ssh>/<private key> <username of host>@<ip address>
```
Example)
```
ssh -p 22 -i ~/.ssh/id_rsa_ansible user_on_host@10.1.0.10
```
Note: Here you need to specify private key, not public key.