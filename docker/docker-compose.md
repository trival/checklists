# Docker compose cheat sheet

## running

```bash
docker-compose up
docker-compose up -d

docker-compose stop # just stop
docker-compose down # stop and remove containers
docker-compose down --volumes # stop and remove containers and volumes

docker-compose ps
docker-compose exec service_name env

docker-compose logs
```

## docker-compose.yaml

### Root values

```yaml
version: '3'
services:
volumes:
networks:
```

### `services`

Maps service name to service configuration

### Service options

```yaml
# Image, Dockerfile or start image
build: .
build:
  context: ./dir
  dockerfile: Dockerfile-name
  args:
    my_build_arg: 1 # Args accessible only during build, correspoinding to ARG Dockerfile instruction

image: 'redis:alpine'

# specify name, otherwise it is generated from project name and service key
container_name: 'my_redis'

ports:
  - '8080:80'

expose:
  - '3000'

env_file: .env # relative to docker-compose file

environment:
  NODE_ENV: 'production' # yaml notation
environment:
  - SERVICE_URL=http://serviceKey:8080 # string list notation

# 'no', 'on-failure', unless-stopped', 'always'
restart: always

volumes:
  - mysql:/var/lib/mysql
  - .:/code # useful with 'build: .' for local dev, no rebuild needed
  - type: volume
    source: mydata
    target: /data
    volume:
      nocopy: true
  - type: bind
    source: ./static
    target: /opt/app/static

command: ["bundle", "exec", "thin", "-p", "3000"] # overrides CMD
entrypoint: # also possible in yaml list form
    - php
    - -d
    - zend_extension=/usr/local/lib/php/extensions/no-debug-non-zts-20100525/xdebug.so
    - -d
    - memory_limit=-1
    - vendor/bin/phpunit


# define startup order
depends_on:
  - de
  - redis

# equivalent to docker run option
user: postgresql
working_dir: /code
domainname: foo.com
hostname: foo
ipc: host
mac_address: 02:42:ac:11:65:43
privileged: true
read_only: true
shm_size: 64M
stdin_open: true
tty: true

healthcheck:
  test: ["CMD", "curl", "-f", "http://localhost"]
  test: curl -f https://localhost || exit 1 # alternative inside shell
  interval: 1m30s
  timeout: 10s
  retries: 3
  start_period: 40s # only compose v3.4+

healthcheck:
  disable: true

init: true # only compose v3.7+

logging:
  driver: "json-file" # only "json-file" and "journald" can be inspected by `docker-compose logs`
  options:
    max-size: "200k"
    max-file: "10"

# links are not recommended. all services in the same network are accessible with hostname same as service key
external_links:
  - mysql:db

deploy: # only used with docker swarm, ignored otherwise
```

### `volumes`, `networks` and others

minimal definition

```yaml
volumes:
  volName: {}
  volName2: # or just empty
  volName3:
    name: foo
```

volume name is prefixed with project name

```yaml
volumes:
  volName:
    external: true
```

for unprefixed name, must be created outside

networks:

```yaml
networks:
  netName:
  outside:
    external: true
  netName2:
    name: bar
```

others

```yaml
configs:
secrets:
```
