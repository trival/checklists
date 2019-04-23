# Dokku configuration

## Nginx timeout

To increase the timeout of nginx, do the following:

```bash
mkdir /home/dokku/gunterlanger/nginx.conf.d/ # if it does not exits
cd /home/dokku/gunterlanger/nginx.conf.d/
echo 'proxy_read_timeout 3600;' > ./timeout.conf # increase timeout to 1 hour
chown dokku:dokku ./timeout.conf
service nginx reload
```

for more info see:
http://dokku.viewdocs.io/dokku/configuration/nginx/#customizing-via-configuration-files-included-by-the-default-tem

## Disable default site

```bash
cd /etc/nginx/conf.d/
vim ./00-default-vhost.conf
```

add following text into the file:

```
server {
    listen 80 default_server;
    server_name _;
    access_log off;
    return 410;
}
```

then

```
service nginx reload
```

for more info see:
http://dokku.viewdocs.io/dokku/configuration/domains/#default-site
