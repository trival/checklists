# Docker

list running processes

```
docker ps
```

list all container, running or stopped

```
docker ps -a
docker ps -aq
```

execute a command inside a running Containers

```
docker exec -it container-name command
```

inspect container

```
docker inspect container-name-or-id
docker inspect container-name-or-id | grep "IPAddress"
```

list all images:

```
docker images
docker images -a
```

## clear

clear all idle containers

```
docker container prune
```

```
docker system prune
```

`prune` can also be called on `volume`, `network`, ...

## build

```
docker build --rm -t trivaldock/php:latest .
```

`--rm` is optional: Remove intermediate containers after successful build
(default true)

publish:

```
docker login

docker push trivaldock/php
```

exclude files from context with `.dockerignore`. Files are ignored by `ADD` and
`COPY`

```docker
# comment
*/temp*
*/*/temp*
**/temp*
temp?
*.md
!README.md
```

## restart policies

- `no`: Do not automatically restart the container. (the default)
- `on-failure`: Restart the container if it exits due to an error, which
  manifests as a non-zero exit code.
- `unless-stopped`: Restart the container unless it is explicitly stopped or
  Docker itself is stopped or restarted.
- `always`: Always restart the container if it stops.

```
docker run --restart unless-stopped
```

`--restart` conflicts with `--rm`

## Some utils

### Copy file from docker container to host

```
docker cp <containerId>:/file/path/within/container /host/path/target
```

works for files or directories
