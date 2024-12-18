FROM node:22.0.0-alpine

# COPY package.json .
# RUN cd nodejs
RUN npm install -g nodemon

WORKDIR /data/nodejs/app
EXPOSE 8081
# VOLUME /data/nodejs/app

# COPY ./ ./data/nodejs/app
COPY package.json .
RUN npm install
CMD ["npm", "run", "dev"]