```
docker pull <image name>
docker run <image name>
```
List containers that are currently running:
```
docker ps
```
List all containers that have been ran:
```
docker ps -a
```
Build a container from image name:
```
docker build -t <remote image name> .
```
Attaches us to an interactive terminal session:
```
docker run -it <image name>
```
Push:
```
docker login
docker push <username>/<image name>
```

