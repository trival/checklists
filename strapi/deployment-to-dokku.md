# Strapi deployment to Dokku

## set domain

point your domains `@` and `*` A and AAAA entries to your server IPs.

then inside dokku

```
dokku domains:set-global <your-domain.com>
```

## create database

ssh as root, or as dokku user and sudo. See:
https://github.com/dokku/dokku-mongo

```
dokku plugin:install https://github.com/dokku/dokku-mongo.git mongo
```

as dokku user

```
MONGO_IMAGE_VERSION="4" dokku mongo:create mongo
```

## add database admin interface app

as dokku user, see
http://dokku.viewdocs.io/dokku~v0.14.2/deployment/methods/images/

```bash
# install mongo-express

dokku apps:create db-admin
docker pull mongo-express
docker tag mongo-express:latest dokku/db-admin:v1
dokku mongo:link mongo db-admin
# save MONGO_URL from dokku mongo:link command

# configure

# set values from MONGO_URL
dokku config:set db-admin \
  ME_CONFIG_MONGODB_ENABLE_ADMIN=false \
  ME_CONFIG_MONGODB_AUTH_USERNAME=mongo \
  ME_CONFIG_MONGODB_AUTH_PASSWORD=b4066bcdcab83df2dce911ee6d090cf1 \
  ME_CONFIG_MONGODB_AUTH_DATABASE=mongo \
  ME_CONFIG_MONGODB_SERVER=dokku-mongo-mongo

dokku domains:set db-admin db-admin.<your-domain.com>

# set auth admin for
dokku config:set db-admin \
  ME_CONFIG_BASICAUTH_USERNAME=admin \
  ME_CONFIG_BASICAUTH_PASSWORD=YourSavePassword

# deploy
dokku tags:deploy db-admin v1
```

### setup ports and ssl

if not yet done, get letsencrypt plugin

```
sudo dokku plugin:install https://github.com/dokku/dokku-letsencrypt.git
```

```bash
# check current ports
dokku proxy:report db-admin

# setup unencrypted port
dokku proxy:ports-add db-admin http:80:8081

# add letsencrypt email
dokku config:set --no-restart db-admin DOKKU_LETSENCRYPT_EMAIL=<e-mail>

# get certificates
dokku letsencrypt db-admin

# set encrypted port
dokku proxy:ports-add db-admin https:443:8081
dokku proxy:ports-remove db-admin http:8081:8081

```

autorenew certificate if necessary, for all apps

```crontab
crontab -e

#!/bin/bash
5 5 * * * dokku letsencrypt:auto-renew >/dev/null 2>&1
```

visit your database `https://db-admin.<your-domain.com>`

IMPORTANT: for security reasons, don't forget to stop the admin once you are
done with administration

```
dokku ps:stop db-admin
```

## Add strapi

from dokku

```
dokku apps:create api
dokku mongo:link mongo api
```

set environment

```
dokku config:set api \
  DATABASE_USERNAME=mongo \
  DATABASE_PASSWORD=b4066bcdcab83df2dce911ee6d090cf1 \
  DATABASE_HOST=dokku-mongo-mongo \
  DATABASE_NAME=mongo \
  DATABASE_AUTHENTICATION_DATABASE=mongo \
  APP_HOST=api.<your-domain.com>
```

from a strapi dev project

create a dokku app named `api`

```
git remote add dokku dokku@<your-domain.com>:api
git push dokku master
```

### setup ports and ssl

```bash

# setup ports
dokku proxy:ports-add api http:80:1337

dokku config:set --no-restart api DOKKU_LETSENCRYPT_EMAIL=<e-mail>
dokku letsencrypt api

dokku proxy:ports-add api https:443:1337
dokku proxy:ports-remove api http:1337:1337
```

### add storage for uploads

```
mkdir -p /var/lib/dokku/data/storage/api/uploads

# change to the containers node user
chown -R 1000:1000 /var/lib/dokku/data/storage/api/uploads

dokku storage:mount api /var/lib/dokku/data/storage/api/uploads:/home/node/app/public/uploads

dokku ps:restart api
```

## Update

on local dev system:

```
git remote add dokku master dokku@<your-domain.com>:api
git push dokku master
```

make local changes using the admin gui. Restart local server after every change
to persist changes.

after all changes are done, push to dokku

If datamode changed, remove `plugin_content-manager_schema` key from
`core_store` collection in mongo db on remote server. Then restart api

```
ssh dokku@<your-domain.com> ps:start db-admin
# make remote changes

ssh dokku@<your-domain.com> ps:restart api

# when you're done
ssh dokku@<your-domain.com> ps:stop db-admin
```

## Debug

enter api container

```
dokku enter api web sh
```
