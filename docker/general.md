# Docker

list running processes

```
docker ps
```

list all container, running or stopped

```
docker ps -a
```

execute a command inside a running Containers

```
docker exec -it container-name command
```

## clear

clear all idle containers

```
docker system prune
```

```
docker container prune
```

`prune` can also be called on `volume`, `network`, ...

## build

```
docker build --rm -t trivaldock/php:latest .
```

publish:

```
docker login

docker push trivaldock/php
```
