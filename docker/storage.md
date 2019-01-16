# Storage options

- _Volumes_: Managed by docker, not writable by external processes
- _Bind mounts_: Arbitary mount point, writable by external processes
- _tmpfs mounts_: Only write to memory

## Bindpoints

docker option:

```
--mount type=bind,source="$(pwd)"/target,destination=/app
```

```
--mount type=bind,source="$(pwd)"/target,target=/app,readonly
```

mount option name aliases:

```
--mount type=bind,src="$(pwd)"/target,dst=/app
```

```
--mount type=bind,source="$(pwd)"/target,target=/app
```

## Volumes

```
docker volume create my-vol
```

```
docker volume ls
```

```
docker volume inspect my-vol
```

```
docker volume rm my-vol
```

mount option:

```
--mount source=my-vol,target=/app
```

## Comments

alternative syntax for mounting exists: `-v` option. Using `--mount` is
recommended by docker docs.

```
-v my-vol:/app
```

```
-v "$(pwd)"/target:/app:ro
```
