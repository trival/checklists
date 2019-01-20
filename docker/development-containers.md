# Containers run in development

## Apache / PHP

see https://hub.docker.com/\_/httpd/ and https://hub.docker.com/\_/php/

```
docker run -d --rm \
	-p 8000:80 \
	-v "$PWD":/var/www/html trivaldock/php \
	--name myphp \
```

runs locally at `localhost:8000`

---

execute commands in running GRAV CMS app:

```
docker exec myphp bin/gpm install antimatter
```

## Nodejs local

```
docker run -it --rm \
	-p 8080:8080 \
	--name node8 \
	--env PORT=8080 \
	-w /app \
	--mount type=bind,src="$PWD",dst=/app \
	node:8-alpine sh
```

to use the local yarn cache, add:

```
--mount type=bind,src=\"$(yarn cache dir)/..\",dst=\"/usr/local/share/.cache/yarn\"
```

## Notes:

- `--rm` removes the container after stop, so the name can be reused
- `-d`: as background daemon
- `-it`: interactive + terminal
- `-p`: `--publish` publish port
- `-w`: current working directory
