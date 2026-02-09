ARG NODE_VERSION=18
FROM node:${NODE_VERSION}-alpine

WORKDIR /app

ENV APP_ENV=production
ENV PORT=3000

COPY package.json ./
RUN npm install
COPY app.js ./

EXPOSE ${PORT}
CMD ["npm", "start"]
