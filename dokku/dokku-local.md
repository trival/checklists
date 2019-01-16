# Dokku local development

setup: see
https://github.com/dokku/dokku/blob/master/docs/getting-started/install/vagrant.md

```
git clone https://github.com/dokku/dokku.git
cd dokku
vagrant up
```

add to `/etc/hosts`

```
10.0.0.2 dokku.me
```

edit ~/.ssh/config

```
Host dokku.me
    Port 22
```

setup ssh key at `http://dokku.me` and set Hostname to dokku.me

```
cat ~/.ssh/id_rsa.pub | pbcopy
```
