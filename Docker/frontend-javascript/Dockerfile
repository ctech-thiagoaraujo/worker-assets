# build stage
FROM node:lts-alpine as build-stage
WORKDIR /app
COPY package*.json yarn.lock tsconfig.json ./
RUN yarn --pure-lockfile
COPY . .
RUN yarn build

# run stage
FROM nginx:stable-alpine as run-stage
COPY --from=build-stage /app/dist/frontend /usr/share/nginx/html
CMD ["/bin/sh", "-c", "sleep 5 && if [ -e \"/vault/secrets/.env\" ] ; then source /vault/secrets/.env ; fi && envsubst < /usr/share/nginx/html/assets/env.template.js > /usr/share/nginx/html/assets/env.js && exec nginx -g 'daemon off;'"]