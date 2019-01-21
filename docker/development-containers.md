# Containers run in development

## Apache / PHP

see https://hub.docker.com/\_/httpd/ and https://hub.docker.com/\_/php/

```
docker run -d --rm \
  -p 8000:80 \
  -v "$PWD":/var/www/html trivaldock/php \
  --name myphp \
```

runs locally at `localhost:8000`

---

execute commands in running GRAV CMS app:

```
docker exec myphp bin/gpm install antimatter
```

## MySQL and phpMyAdmin

with docker-compose

```yaml
version: '3'
services:
  app:
    build: .
    depends_on:
      - mysql

  mysql:
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: `12345`
    volumes:
      - mysql:/var/lib/mysql
    ports:
      - 3306:3306

  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    container_name: phpmyadmin
    environment:
      - PMA_ARBITRARY=1
    restart: always
    ports:
      - 8080:80
    volumes:
      - sessions:/sessions
    depends_on:
      - mysql

volumes:
  mysql:
  sessions:
```

enter phpMyAdmin with

    host: mysql # equal to docker-compose service key
    user: root
    password: 12345

## Nodejs local

```
docker run -it --rm \
  -p 8080:8080 \
  --name node8 \
  --env PORT=8080 \
  -w /app \
  --mount type=bind,src="$PWD",dst=/app \
  node:8-alpine sh
```

to use the local yarn cache, add:

```
--mount type=bind,src=\"$(yarn cache dir)/..\",dst=\"/usr/local/share/.cache/yarn\"
```

## Notes:

- `--rm` removes the container after stop, so the name can be reused
- `-d`: as background daemon
- `-it`: interactive + terminal
- `-p`: `--publish` publish port
- `-w`: current working directory
