# Dokku troubleshooting

## letsencrypt not updating

```
dokku letsencrypt:auto-renew
dokku letsencrypt:cron-job --add
```
