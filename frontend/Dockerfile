# build env
FROM node:21.7.1-alpine

WORKDIR /app

COPY package*.json ./
RUN npm install --force

COPY . .

EXPOSE 3000

CMD ["npm", "start"]
