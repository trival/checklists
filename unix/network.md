# Network utils for unix command line

## Check for network

```bash
ping google.com # only for domains
nc -vz google.com 80 # checks for port
```

## HTML requests

show content in terminal, don't download

```bash
wget -qO- http://localhost:8082
curl localhost:8081
```
