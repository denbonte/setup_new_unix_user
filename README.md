# Setup a New Linux User

A few notes on the setup process for a new user under Linux/UNIX systems.


## User Creation and Setup

Create a new user `$USER` by running:

```
sudo useradd $USER
```

After setting up a user password for `$USER`:

```
sudo passwd $USER
```

Make sure to create a `home` directory at the selected location `$USER_HOME` by running:

```
sudo mkdir $USER_HOME
sudo cp -rT /etc/skel $USER_HOME
sudo usermod -d $USER_HOME $USER
sudo chown -R $USER:$USER $USER_HOME
```

To set a default shell for `$USER`, run:

```
sudo usermod --shell /bin/bash $USER
```

## Adding Users to a Group/Group Management

To create a new group `$GROUP`, run:

```
sudo groupadd $GROUP
```

To add `$USER` to `$GROUP`, run:

```
sudo usermod -a -G $GROUP $USER
```

To change the group and user ownership of a folder/file `$FILE` to that of `$USER` and `$GROUP`, run:

```
sudo chown -R $USER:$GROUP $FILE
```

To change the group ownership of a folder/file `$FILE` to that of `$GROUP`, run:

```
sudo chgrp -R $GROUP $FILE
```

## Setup SSH-key Only Access for a User

After logging in with the desired user, to generate an (RSA 4096) SSH keypair {`$KEY`, `$KEY.pub`} run:

```
ssh-keygen -t rsa -b 4096 -f $USER_HOME/.ssh/$KEY
```

To turn on and off the password/SSH publickey access, edit the associated lines in the SSH daemon config file and then restart the daemon:

```
sudo nano /etc/ssh/sshdconfig
sudo service ssh restart
```

After the keypair is generated, turn off the publickey only authentication. Using the terminal on the machine you want to install the key on, connect to the remote server. After copying the private key locally (either SSH'ing and copying the content of the private key file, or using `scp` - e.g., `scp $USER@$IP_ADDR_REMOTE:$USER_HOME/.ssh/$KEY $LOCAL_HOME/.ssh`), run the following command:

```
ssh-copy-id -i $LOCAL_HOME/.ssh/$KEY $USER@$IP_ADDR_REMOTE
```

After this operation, turn off the with-password access on the remote host.
After rebooting the SSH daemon on the remote machine, the following command should not work anymore (and give the error):

```
ssh $USER@$IP_ADDR_REMOTE

>> $USER@$IP_ADDR_REMOTE: Permission denied (publickey).
```

On the contrary, the following should:

```
ssh $USER@$IP_ADDR_REMOTE -i $LOCAL_HOME/.ssh/$KEY
```

## Sources

[The Complete Guide to “useradd” Command in Linux](https://www.tecmint.com/add-users-in-linux/)
[Managing Users on Linux Systems](https://www.networkworld.com/article/3225109/managing-users-on-linux-systems.html)
[SSH Keygen](https://www.ssh.com/ssh/keygen/)
[Force SSH to only allow users with a key to log in](https://askubuntu.com/questions/346857/how-do-i-force-ssh-to-only-allow-users-with-a-key-to-log-in)
[Copy SSH Keys to a Remote Host](https://askubuntu.com/questions/4830/easiest-way-to-copy-ssh-keys-to-another-machine)

