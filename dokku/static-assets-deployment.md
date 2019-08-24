# deploy a static website to dokku

## Static buildpack

See: https://johnfraney.ca/posts/2019/03/01/build-deploy-static-site-dokku/

1. Create the app

   ```
   dokku apps:create app-name
   ```

2. Specify host directory, if it is not the repository root, for example
   `static`

   ```
   dokku config:set app-name NGINX_ROOT=/app/www/static
   ```

3. Add a empty `.static` file in the root of the repository

4. If there is a package.json file, add a `.buildpacks` file to block automatic
   nodejs app buildpack from starting, with following content:

   ```
   https://github.com/dokku/buildpack-nginx.git#v12
   ```

   get latest buildpack version from github

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

CMD ["serve", "-l", "5000", "-n"]
```

Usefull serve options:

- `-l, --listen`: listen, port or uri to expose
- `-s, --single`: Rewrite all not-found requests to `index.html`
- `-n, --no-clipboard`: Do not copy the local address to the clipboard

ignore files to copy with `.dockerignore` file.

```
node_modules
.DS_Store
*.log
.env

src
.editorconfig
.prettierrc
package.json
tsconfig.json
tslint.json
types.d.ts
yarn.lock
```

# docker-compose.yml for local development with the Dockerfile approach

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
