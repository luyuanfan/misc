## Flags
`-v` is for verbose debugging outputs

---
## SSH
Use `-i` to specify which private key to use when connecting to a remote server ([Link](https://serverfault.com/questions/295768/how-do-i-connect-to-ssh-with-a-different-public-key)). 
When specifying which key pair to use, go to `.ssh/config` and add 
```
Host <hostname>
	HostName <hostname or ip>
	IdentityFile path/to/privatekey
```

---
## Installing things on linux

Run `sudo apt install <packagename>`
If not root user, put things in `~/.local/`
```
$ ./configure PREFIX=$HOME/.local
```

---
## Misc

Check operating system version by
```
cat /etc/os-release
```

Make a running process to run in background instead:
```
press control + z to suspend process
$ bg
```

If want to run something in the background and keep it running even if I've logged out of SSH, and have it in the background, do:
```
$ nohup <command> &
```

Change current user's password:
```
$ passwd
```

The `var` directory stores things that will likely change a lot, such as website and databases. 

Make a python environment
```
$ python3 -m venv path/to/venv
$ source venv/bin/activate
$ pip3 install things
```

How to find a file in an entire machine:
```
$ find / -name "filename"
```
