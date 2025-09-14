Can list [Unix Domain Sockets](https://serverfault.com/questions/124517/what-is-the-difference-between-unix-sockets-and-tcp-ip-sockets) by: `netstat -a -p --unix
- It's a socket that allows processes on the same machine to communicate with kernel space, so a lot less operations like routing need to happen. (So different from TCP/IP)
