FROM node:14-slim

WORKDIR /app

COPY package.json .

RUN npm install

COPY . .

#VOLUME ["/app/node_modules"]

EXPOSE 80

CMD ["npm", "start"]