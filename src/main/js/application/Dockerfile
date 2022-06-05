#Stage 1: Build
FROM node:12.7-alpine AS build
WORKDIR /usr/src/app
COPY package.json package-lock.json ./
COPY . .
RUN npm install
RUN npm run build

#Stage 2: Run
#FROM nginx:1.17.1-alpine
#COPY nginx.conf /etc/nginx/nginx.conf
#COPY /dist/application /usr/share/nginx/html