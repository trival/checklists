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

execute commands in running GRAV CMS app:

```
docker exec myphp bin/gpm install antimatter
```

---

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

---

## Postgres

local test env setup

```
docker run -d --rm \
  -p 5432:5432 \
  -h postgres \
  --name postgres \
  -e POSTGRES_PASSWORD=12345 \
  -v postgres_local:/var/lib/postgresql/data \
  postgres

docker run --rm -d \
  -p 8080:80 \
  --name pgadmin \
  -v pgadmin_local:/var/lib/pgadmin \
  -e "PGADMIN_DEFAULT_EMAIL=pgadmin@pg.com" \
  -e "PGADMIN_DEFAULT_PASSWORD=12345" \
  --link postgres \
  dpage/pgadmin4
```

or in docker-compose

```yaml
postgres:
  image: postgres
  posts:
    - 5432:5432
  environment:
    - POSTGRES_PASSWORD: '12345'
  volumes:
    - postgres:/var/lib/postgresql/data

pgadmin:
  image: dpage/pgadmin4
  restart: always
  ports:
    - 9090:80
  volumes:
    - pgadmin:/var/lib/pgadmin
  environment:
    - PGADMIN_DEFAULT_EMAIL=pgadmin@pg.com
    - PGADMIN_DEFAULT_PASSWORD=12345
```

---

## MongoDB

```yaml
# Use root/example as user/password credentials
version: '3.1'

services:
  mongo:
    image: mongo
    restart: always
    environment:
      MONGO_INITDB_ROOT_USERNAME: root
      MONGO_INITDB_ROOT_PASSWORD: example

  mongo-express:
    image: mongo-express
    restart: always
    ports:
      - 8081:8081
    environment:
      ME_CONFIG_MONGODB_ADMINUSERNAME: root
      ME_CONFIG_MONGODB_ADMINPASSWORD: example
    depends_on:
      - mongo

  admin-mongo:
    image: mrvautin/adminmongo
    restart: always
    ports:
      - 8082:8082
    environment:
      PORT: '8082'
      HOST: '0.0.0.0' # important to access it on the outside
      PASSWORD: 12345
      DB_HOST: mongo
      DB_USER: root
      CONN_NAME: default
      DB_PASSWORD: example
    depends_on:
      - mongo
```

---

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
