
FROM node:10-alpine

RUN mkdir -p /home/node/app && chown -R node:node /home/node/app

WORKDIR /home/node/app

COPY . .

USER node

RUN npm install

EXPOSE 3000

CMD [ "node", "index.js" ]
