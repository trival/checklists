# Dokku app deployment

## Prerequisites

Make shure that all ssh users are added to dokku (see troubleshooting below)

```
cat ~/.ssh/id_rsa.pub | ssh root@<dokku-domain.com> dokku ssh-keys:add KEY_NAME
```

Install usefull plugins (ssh as root)

```
dokku plugin:install https://github.com/dokku/dokku-letsencrypt.git
dokku plugin:install https://github.com/dokku/dokku-redirect.git
dokku plugin:install https://github.com/dokku/dokku-http-auth.git
```

set your email for letsencrypt

```bash
# global
dokku config:set --global DOKKU_LETSENCRYPT_EMAIL=<e-mail>

# or local
dokku config:set --no-restart app-name DOKKU_LETSENCRYPT_EMAIL=<e-mail>
```

add letsencrypt auto renew cron job

```
dokku letsencrypt:cron-job --add
```

If you have git submodules inside your app container, you probably need to
provide ssh keys for your git provider

```
dokku plugin:install https://github.com/cedricziel/dokku-deployment-keys.git deployment-keys
```

## App setup

1. Create valid Dockerfile.

   Don't use `EXPOSE \${PORT}`. Dokku parses the sting after "EXPOSE" and uses
   as port.

2. Create a new app by pushing to dokku.

   ```
   git remote add dokku dokku@dokku.me:app-name
   git push dokku master
   ```

   `app-name` is automatically created

3. SSH into dokku server

4. Set restart-policy to 'always' and restart to take effect

   ```
   dokku ps:set-restart-policy app-name always
   dokku ps:restart app-name
   ```

5. Set environment variables

   ```bash
   dokku config:set app-name \
     SOME_VAR1=<some-value> \
     SOME_VAR2=<some-value>
   ```

6. Add domains to your app

   ```bash
   dokku domains:set app-name your-domain.com
   # add more if needed
   dokku domains:add app-name www.your-domain.com
   ```

7. Configure network

   ```bash
   dokku proxy:ports-add app-name http:80:3000
   dokku proxy:ports-remove app-name http:3000:3000
   dokku letsencrypt app-name
   dokku proxy:report

   # maybe unneeded (added automatically by letsencrypt plugin)
   # dokku proxy:ports-add db-admin https:443:8081

   # optionally redirect to www
   dokku redirect:set your-domain.com www.your-domain.com

   # enable aute-renew
   dokku letsencrypt:auto-renew app-name

   # optional: enable basic http auth
   dokku http-auth:on app-name username password
   ```

   (add all domains before running letsencrypt, or repeat)

## Useful commands

```
dokku apps:list
dokku ps:report app-name
dokku logs app-name
```

### Deploy from a different local branch than master

see:
http://dokku.viewdocs.io/dokku/deployment/methods/git/#changing-the-deploy-branch

```bash
# where `SOME_BRANCH_NAME` is the name of the local branch
git push dokku SOME_BRANCH_NAME:master
```

## Debug

### Enter app container

```bash
dokku enter app-name # if bash is available in the conainer
dokku enter app-name web sh # for pure sh access
```

add bash to alpine images in Dockerfile if needed

```Dockerfile
RUN apk add bash
```

## Troubleshooting

make shure your ssh key is added by `dokku ssh-keys:add`, not just copied.
`ssh dokku@<your-server>` must run the `dokku` command, not grant you shell
access! Otherwise git will not work

```bash
# from local maschine
cat ~/.ssh/id_rsa.pub | ssh root@dokku.me dokku ssh-keys:add KEY_NAME

# or

# from root ssh terminal
# but works only if one key in authorized_keys
cat /root/.ssh/authorized_keys | ssh root@dokku.me dokku ssh-keys:add KEY_NAME
```

This musst happen with every ssh key separately for every connection device.
