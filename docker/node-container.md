# Node docker containers

Best practices:
https://github.com/nodejs/docker-node/blob/master/docs/BestPractices.md

## docker-compose.yml example

```yaml
version: '2'
services:
  node:
    image: 'node:8'
    user: 'node'
    working_dir: /home/node/app
    environment:
      - NODE_ENV=production
    volumes:
      - ./:/home/node/app
    expose:
      - '8081'
    command: 'npm start'
```

```
docker-compose up -d
```

## Example run

```
docker run \
	-e "NODE_ENV=production" \
	-u "node" \
	-m "300M" --memory-swap "1G" \
	-w "/home/node/app" \
	-v $(pwd):/home/node/app \
	--name "my-nodejs-app" \
	--init \
	--rm \
	-e PORT=3000 \
	-p 8080:3000 \
	node:8-alpine node [script]
```

## Best practices

### set `NODE_ENV` to `production`

```
-e "NODE_ENV=production"
```

### Global npm dependencies

```Dockerfile
ENV NPM_CONFIG_PREFIX=/home/node/.npm-global
ENV PATH=$PATH:/home/node/.npm-global/bin # optionally if you want to run npm global bin without specifying path
```

### start docker with `--init` option!

### use user `node`

```Dockerfile
USER node
```

```
-u "node"
```

### limit memory

```
-m "300M" --memory-swap "1G"
```

### Check security with https://github.com/docker/docker-bench-security

### node-gyp support on alpine images

```Dockerfile
FROM node:alpine

RUN apk add --no-cache --virtual .gyp python make g++ \
    && npm install [ your npm dependencies here ] \
    && apk del .gyp
```

multistage build example

```Dockerfile
FROM node:alpine as builder

## Install build toolchain, install node deps and compile native add-ons
RUN apk add --no-cache --virtual .gyp python make g++
RUN npm install [ your npm dependencies here ]

FROM node:alpine as app

## Copy built node modules and binaries without including the toolchain
COPY --from=builder node_modules .
```
