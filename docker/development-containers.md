# Containers run in development

## Apache / PHP

see https://hub.docker.com/\_/httpd/ and https://hub.docker.com/\_/php/

```
docker run -d --rm -p 8000:80 --name myphp -v "$PWD":/var/www/html php:7.2-apache
```

runs locally at `localhost:8000`

## Nodejs local

```
docker run -it --rm -p 8080:8080 --name node8 --env PORT=8080 -w /app --mount type=bind,src="$PWD",dst=/app node:8-alpine sh
```

## Notes:

- `--rm` removes the container after stop, so the name can be reused
- `-d`: as background daemon
- `-it`: interactive + terminal
- `-p`: `--publish` publish port
- `-w`: current working directory
