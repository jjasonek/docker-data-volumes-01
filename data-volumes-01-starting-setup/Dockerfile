FROM node:14-slim

WORKDIR /app

COPY package.json .

RUN npm install

COPY . .

# set ENV variable name and default value
ENV PORT 80

EXPOSE $PORT

CMD ["npm", "start"]