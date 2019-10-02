# Deploying from a Docker registry

Copied from
http://dokku.viewdocs.io/dokku/deployment/methods/images/#deploying-from-a-docker-registry

You can add an image pulled from a Docker registry and deploy from it by using
tagging feature. In this example, we are deploying from Docker Hub.

1. Create Dokku app as usual.

   ```
   dokku apps:create test-app
   ```

2. Pull image from Docker Hub.

   ```
   docker pull demo-repo/some-image:v12
   ```

3. Retag the image to match the created app.

   ```
   docker tag demo-repo/some-image:v12 dokku/test-app:v12
   ```

4. Deploy tag.

   ```
   dokku tags:deploy test-app v12
   ```
