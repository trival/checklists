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
