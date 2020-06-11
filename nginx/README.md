# Usage
You may need sudo at the top of the following command, or switching to root user by sudo su, or you can [enable docker commands without sudo command.](https://docs.docker.com/engine/install/linux-postinstall/)
```
$ docker run -v <full-path-to-log-dir>:/var/log/nginx <full-path-to-local-conf.d>:/etc/nginx/conf.d -p <ssh-port-host-use>:22 -it nginx
```

Now you can ssh login to the container as usual.
```
$ ssh -p <ssh-port-host-use> root@<ip address>
```

## Example (Username is foo)
If you run docker container on the same pc, you do the follow firstly in the terminal on your pc.
```
$ docker run -v /home/foo/work/nginx/log:/var/log/nginx /home/foo/work/nginx/conf.d:/etc/nginx/conf.d -p 20022:22 -it nginx
```
And then, do the follow to ssh login to ssh login to the running docker.
```
$ ssh -p 20022 root@127.0.0.1
```
You can replace 127.0.0.1 with the ip address you can check by ifconfig command.
You'll see something like "docker0" and can use ip address shown on it.


## Trouble shooting
* Any typos?
* Interact with the running docker container by -it option or /bin/bash),
  and check if sshd is running by "ps aux".
* Did you correctly specify port number when doing ssh?(Check -p option and its value)
* Mostly case-sensitive. So be careful not to use -P when calling ssh. (On the other hand, scp uses uppercase(-P) to specify port number to use)
* Check docker containers are running correctly by "docker container ls".
