# Local Dokku development

## Installation process:

Start with bash inside ubuntu:18.04 image:

```
docker run -it --rm --name dokku --privileged -p 22 -p 80 -p 443 --mount src=dokku_vol1,dst=/home/dokku --mount type=bind,src=/var/run/docker.sock,dst=/var/run/docker.sock ubuntu:18.04 bash
```

```
docker exec -it dokku bash
```

inside the image

```
cd
apt-get update
apt-get install -y wget
wget https://raw.githubusercontent.com/dokku/dokku/v0.14.2/bootstrap.sh;
DOKKU_TAG=v0.14.2 bash bootstrap.sh
```

for install script see:
http://dokku.viewdocs.io/dokku/getting-started/installation/

`--mount type=bind,src=/var/run/docker.sock,dst=/var/run/docker.sock` is needed
for successfull installation. It binds local docker sock so it is not docker in
docker, but dokku using the host docker instead. See
https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/

## NOTE

The installation finishes, but setup is not reachable. No success so far running
Dokku locally within docker. Trying Vagrant as an alternative.
