# Dockerfile reference

See https://docs.docker.com/engine/reference/builder/

Best practices:
https://docs.docker.com/develop/develop-images/dockerfile_best-practices/

`RUN`, `COPY` and `ADD` create new image layers.

## FROM

required, specify the parent container, must be first

```Dockerfile
FROM ubuntu:latest
```

Only `ARG` can come before `FROM`

```Dockerfile
ARG  CODE_VERSION=latest
FROM base:${CODE_VERSION}
```

## RUN

executes shell command with `/bin/sh -c`

```Dockerfile
RUN apt-get update && apt-get install -y \
        package-bar \
        package-baz \
        package-foo=1.3.* \
&& rm -rf /var/lib/apt/lists/*
```

always run `apt-get update` and `apt-get install` inside the same `RUN`

optionally clear apt cache by `&& rm -rf /var/lib/apt/lists/*` to reduce image
size

for non-shell commands use the _exec_ form

```Dockerfile
RUN ["/bin/bash", "-c", "echo hello"]
```

## COPY

```Dockerfile
COPY hom* /mydir/        # adds all files starting with "hom"
COPY hom?.txt /mydir/    # ? is replaced with any single character, e.g., "home.txt"

COPY test relativeDir/   # adds "test" to `WORKDIR`/relativeDir/
COPY test /absoluteDir/  # adds "test" to /absoluteDir/

COPY --chown=55:mygroup files* /somedir/
COPY --chown=bin files* /somedir/
COPY --chown=1 files* /somedir/
COPY --chown=10:11 files* /somedir/
```

## ADD

same as `COPY`.

Allows remote URLs as `<src>`

Additionally extracts local archives from `<src>` to `<dest>`

```Dockerfile
ADD http://example.com/foobar /
```

prefer `COPY`, if you don't need the special properties of `ADD`

## WORKDIR

```Dockerfile
WORKDIR /path/to/workdir
```

sets the working directory for any `RUN`, `CMD`, `ENTRYPOINT`, `COPY` and `ADD`
instructions that follow it

! prefer `WORKDIR` over `RUN cd … && do-something`

## USER

```Dockerfile
FROM microsoft/windowsservercore
# Create Windows user in the container
RUN net user /add patrick
# Set it for subsequent commands
USER patrick
```

```Dockerfile
RUN groupadd -r postgres && useradd --no-log-init -r -g postgres postgres
USER postgres
```

Use [gosu](https://github.com/tianon/gosu) instead of `sudo`

## ENV

set env variables inside the container.

```Dockerfile
ENV myName="John Doe" myDog=Rex\ The\ Dog \
    myCat=fluffy
```

equals to

```Dockerfile
ENV myName John Doe
ENV myDog Rex The Dog
ENV myCat fluffy
```

can also be reused inside the Dockerfile

```Dockerfile
ENV foo /bar
WORKDIR ${foo}   # WORKDIR /bar
ADD . $foo       # ADD . /bar
```

can be overridden by `docker run --env <key>=<value>`

for single command env var use

```Dockerfile
RUN <key>=<value> <command>
```

examples

```Dockerfile
ENV PATH /usr/local/nginx/bin:$PATH
```

can be replaced in any of following instructions:

    ADD COPY ENV EXPOSE FROM LABEL STOPSIGNAL USER VOLUME WORKDIR

## ARG

```Dockerfile
ARG user1
ARG user2=someuser
USER $user2
USER ${user1:-foo} # set to foo if user1 undefined
```

can be set by

```
docker build --build-arg user=what_user .
```

## EXPOSE

indicate which ports are available, mainly documentation purpose. With optional
protocol `tcp` or `udp`. Needs still to be published by `docker run -p`

```Dockerfile
EXPOSE 80/tcp
```

## VOLUME

creates a volume.

```Dockerfile
VOLUME ["/var/log", "/var/db"]
VOLUME /var/log /var/db
```

Volumes must be created after a directory is modified by `RUN`, `COPY` or `ADD`.

```Dockerfile
FROM ubuntu
RUN mkdir /myvol
RUN echo "hello world" > /myvol/greeting
VOLUME /myvol
```

If you want some specific host directory or volume name, you need to specify it
by `docker run`

## CMD

default command to execute. Specified arguments to `docker run` override this

```Dockerfile
CMD ["/usr/bin/wc","--help"]
```

## ENTRYPOINT

same as `CMD`, but args to `docker run` are appended, not overridden. Container
is used as executable

default args can be provided by `CMD`

can be overridden by `docker run --entrypoint`

```Dockerfile
FROM debian:stable
RUN apt-get update && apt-get install -y --force-yes apache2
EXPOSE 80 443
VOLUME ["/var/www", "/var/log/apache2", "/etc/apache2"]
ENTRYPOINT ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]
```

## LABEL

adds metadata to an image

```Dockerfile
LABEL "com.example.vendor"="ACME Incorporated"
LABEL com.example.label-with-value="foo"
LABEL version="1.0"
LABEL maintainer="SvenDowideit@home.org.au"
LABEL description="This text illustrates \
that label-values can span multiple lines."
```

can be read by `docker inspect`

## SHELL

change the default shell used by `RUN`, `CMD` and `ENTRYPOINT`

```Dockerfile
FROM microsoft/nanoserver
SHELL ["powershell","-command"]
```

## HEALTHCHECK

The `HEALTHCHECK` instruction tells Docker how to test a container to check that
it is still working.

checks every five minutes or so that a web-server is able to serve the site’s
main page within three seconds:

```Dockerfile
HEALTHCHECK --interval=5m --timeout=3s \
	CMD curl -f http://localhost/ || exit 1
```

options:

- --interval=DURATION (default: 30s)
- --timeout=DURATION (default: 30s)
- --start-period=DURATION (default: 0s)
- --retries=N (default: 3)

disable any healthcheck inherited from the base image

```Dockerfile
HEALTHCHECK NONE
```

## ONBUILD

The ONBUILD instruction adds to the image a trigger instruction to be executed
at a later time, when the image is used as the base for another build. The
trigger will be executed in the context of the downstream build, as if it had
been inserted immediately after the FROM instruction in the downstream
Dockerfile.

## STOPSIGNAL
