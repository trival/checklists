FROM node:10.15.0-alpine

ENV NODE_ENV production

COPY . /home/node/app

RUN chown -R node:node /home/node/app

WORKDIR /home/node/app

USER node

RUN npm install --only=production

EXPOSE 1337

HEALTHCHECK --interval=15s --timeout=5s --start-period=30s \
  CMD node healthcheck.js

CMD ["node", "server.js"]

