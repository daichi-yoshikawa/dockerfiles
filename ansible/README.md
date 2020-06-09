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
docker run -v <full-path-to-ssh-dir>:/home/<username of docker>/.ssh -v <full-path-to-root-of-playbook>:/home/<username of docker>/playbooks -it ansible
```
For example, the above will be like below(Default username is "ansible").
```
docker run -v /home/user_on_host/tmp/.ssh:/home/ansible/.ssh -v /home/user_on_host/work/playbooks/my-web-server:/home/ansible/playbooks -it ansible
```

If you need to put docker container in the same network as
the host machine's, you can do as below.
```
docker run -v <full-path-to-ssh-dir>:/home/username of docker>/.ssh -v <full-path-to-root-of-playbook>:/home/<username of docker>/playbooks --network=host -it ansible
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
Example1)
```
ssh -p 22 -i ~/.ssh/id_rsa_ansible user_on_host@10.1.0.10
```
Note: Here you need to specify private key, not public key.

## Tips
### Working with Vagrant virtual machine
You may want to use virtual machine as remote server to test your ansible scripts. In such a case, you need to be careful of way to point IP address of the virtual machine. Assuming that virtual machine is launched based on the following config, ...
```
...
config.vm.box = "bento/ubuntu-18.04"
config.ssh.password = "vagrant"
config.ssh.forward_agent = true
config.vm.define :node1 do |node1|
  node1.vm.hostname = "test"
  node1.vm.network :forwarded_port, guest:22, host:10022
  node1.vm.network :private_network, ip: "192.168.10.1"
...
```
... and launch virtual machine.
```
vagrant up
```
If you've already launched virtual machine(s) and would like to start everything over, the below may help.
```
vagrant halt && vagrant destroy -f && vagrant up
```

With those configuration, hosts file will be like below.
```
<any name you like> ansible_ssh_host=192.168.10.1 ansible_ssh_port=10022 ansible_ssh_user=vagrant ansible_ssh_private_key_file=<full-path-to-private-key on host pc(ansible server)>
```

If it fails with error message like "Permission Denied", you can try the follows.
* Typo?
* Port settings are corresponding?
* Did you correctly run docker container with "--network=host"?
* Re-try with ansible_ssh_host=127.0.0.1?
* Is there "authorized_keys" file, which is a file renamed from public key(Not private key) associated with the ansible server?
* Check permissions of public key and private key. You can check permissions by ls -la. Both are usually 600. So try "chmod 600 <public key or private key>".




