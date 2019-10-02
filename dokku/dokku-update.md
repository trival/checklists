# Steps to update a dokku instance

1. run `dokku-update`

2. Find installed plugins and update them

   **plugins are updated by `dokku-update` automatically**

   in case you want to double check:

   ```
   dokku plugin:list
   dokku plugin:update mongo
   dokku plugin:update letsencrypt
   ```
