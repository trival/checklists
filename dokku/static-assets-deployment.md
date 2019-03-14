# deploy a static website to dokku

## Dockerfile

```Dockerfile
FROM node:10-alpine

ENV NODE_ENV production
ENV PORT 5000

COPY --chown=node:node . /home/node/app

WORKDIR /home/node/app

RUN yarn global add serve

USER node

EXPOSE 5000

CMD ["serve", "-l", "5000"]
```

ignore files to copy with `.dockerignore` file.

## docker-compose.yml

```yaml
version: '3'

services:
  www:
    build: .
    ports:
      - 5000:5000
    restart: 'no'
    volumes:
      - .:/home/node/app
```
