FROM node:20-alpine AS builder

# Setting up the work directory
WORKDIR /app

# Installing dependencies
COPY ./package.json  ./
RUN npm install

# Copying all the files in our project
COPY . .

# Building our application
RUN npm run build

# Fetching the latest nginx image
FROM nginx

# Copying built assets from builder
COPY --from=builder /app/dist /usr/share/nginx/html

# Copying our nginx.conf
COPY --from=builder /app/docker/prod/nginx.conf /etc/nginx/conf.d/default.conf