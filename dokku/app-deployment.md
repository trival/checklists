# Dokku app deployment

1. create valid Dockerfile.

   Don't use EXPOSE \${PORT}. Dokku parses the sting after "EXPOSE" and uses as
   port.

2. create a new app by pushing to dokku.

   ```
   git remote add dokku dokku@dokku.me:app-name
   git push dokku master
   ```

   `app-name` is automatically created

3. SSH into dokku server

4. set restart-policy to 'always' and restart to take effect

   ```
   dokku ps:set-restart-policy app-name always
   dokku ps:restart app-name
   ```

## Useful commands

```
dokku apps:list
dokku ps:report app-name
dokku logs app-name
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
