# Deploy and configure hasura on dokku

Check if you have a swapfile on the server (digital ocean)

First follow the guide to install an docker-registry image as an app in dokku

```
dokku apps:create hasura
docker pull hasura/graphql-engine:v1.0.0-beta.6
docker tag hasura/graphql-engine:v1.0.0-beta.6 dokku/hasura:1.0.0.6
dokku ps:set-restart-policy hasura always
```

don't deploy yet without having the environment configured

## Add pgadmin to manage postgress

```
dokku apps:create pgadmin
docker pull dpage/pgadmin4:4.13
docker tag dpage/pgadmin4:4.13 dokku/pgadmin:4.13
dokku config:set pgadmin PGADMIN_DEFAULT_EMAIL=<your-email>
dokku config:set pgadmin PGADMIN_DEFAULT_PASSWORD=<yourPassword>
dokku config:set pgadmin PGADMIN_LISTEN_PORT=5000
dokku tags:deploy pgadmin 4.13
```

!!IMPORTANT

- Set a strong password
- shut down pgadmin when you are done administrating!

```bash
dokku ps:stop pgadmin #important!
```

## Configure environment variables

1. Configure database string

   for Digital Ocean private network, use something like

   ```
   dokku config:set hasura HASURA_GRAPHQL_DATABASE_URL=postgresql://user:password@private-db-postgresql-fra1-48493-do-user-6568053-0.db.ondigitalocean.com:25060/defaultdb?sslmode=require
   ```

2. Configure port to 5000 (Dokku default)

   ```
   dokku config:set hasura HASURA_GRAPHQL_SERVER_PORT=5000
   ```

3. Enable console if desired

   ```
   dokku config:set hasura HASURA_GRAPHQL_ENABLE_CONSOLE=true
   ```

4. Secure endpoint with a admin secret

   ```
   dokku config:set hasura HASURA_GRAPHQL_ADMIN_SECRET=myadminsecretkey
   ```

after setting variables, deploy hasura app

```
dokku tags:deploy hasura 1.0.0.6
```

## add domain for public access

## alternatively, add network plugin

See https://github.com/ithouse/dokku-connect-network/blob/master/README.md
