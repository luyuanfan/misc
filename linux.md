## network

to set static ip address ([tutorial](https://gal.vin/posts/2023/ubuntu-static-ip/))

to understand the output of `ip addr` ([article](https://samuel-ricky.medium.com/how-to-interpret-the-output-of-ip-addr-show-8008c7c41dde))
 
if wifi is not connecting, there might be malformed configurations in netplan (this might not target the cause but it fixes the troubles): 
```
sudo cp /etc/netplan/*.yaml ~/network-backup/

sudo rm /etc/netplan/*.yaml

sudo netplan apply
```

## general

loop back addresses - special ip addresses for sending packets across the same machine. conventionally the entire `127.0.0.0` to `127.255.255.255` range is reserved for this. 
## ssh

Do:
```
ssh-keygen -t ed25519 -C "your_email@example.com"
```

- `-t` specify what kind of key pair generating algorithm to use. 
- `-C` stands for comment. 

Use `-i` to specify which private key to use when connecting to a remote server ([Link](https://serverfault.com/questions/295768/how-do-i-connect-to-ssh-with-a-different-public-key)), but honestly that sounds sketch and just use the following:

Go to `~/.ssh/config` and add:
```
Host <hostname>
	HostName <hostname or ip>
	IdentityFile path/to/private/key
```

---
## installing things on linux

1. If not root user, put things in `~/.local/` (usually) with `PREFIX=$SOMEWHERE`

2. There's a few things:
```
sudo apt update
sudo apt install
sudo apt upgrade
sudo apt remove <packagename>
sudo apt purge <packagename>
apt search <packagename>
apt show <packagename>
```

- `remove` does delete but keeps the config files (if some customized settings were made)
- `purge` on the other hand deletes fully everything 

3. The `var` directory stores things that will likely change a lot, such as website and databases. 

4. PPA (personal package archive) - is a repository supported by ubuntu where users can install packages outside of official ubuntu repositories. 
```
sudo add-apt-repository ppa:<pathtotherepo>
sudo apt-get update
sudo apt-get install
```

---
## misc commands

`-v` is for verbose debugging outputs

Check operating system version by
```
cat /etc/os-release
```

Make a running process to run in background instead:
```
## press ctrl + z to halt process
$ bg
```

If want to run something in the background and keep it running even if logged out of SSH, and have it in the background, do:
```
$ nohup <command> &
```

Change current user's password:
```
$ passwd
```

Make a python environment
```
$ python3 -m venv path/to/venv
$ source venv/bin/activate
$ pip3 install things
```

How to find a file:
```
$ find -name "filename"
```