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
## Installing things on linux

If not root user, put things in `~/.local/` (usually) with `PREFIX=$SOMEWHERE`

There's a few things:
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

The `var` directory stores things that will likely change a lot, such as website and databases. 

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