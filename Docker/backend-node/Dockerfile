# build stage
FROM node:lts-alpine as build-stage
WORKDIR /app
COPY package*.json yarn.lock tsconfig.json ./
RUN apk add --no-cache build-base g++ cairo-dev jpeg-dev pango-dev giflib-dev
RUN yarn --pure-lockfile
COPY . .
RUN yarn build
# RUN yarn test

# run stage
FROM node:lts-alpine as run-stage
ENV TZ=America/Sao_Paulo
RUN apk add --no-cache build-base g++ cairo-dev jpeg-dev pango-dev giflib-dev
RUN apk --update add tzdata && \
cp /usr/share/zoneinfo/America/Sao_Paulo /etc/localtime && echo America/Sao_Paulo > /etc/timezone
WORKDIR /app
COPY package*.json yarn.lock ./
RUN yarn --pure-lockfile
COPY --from=build-stage /app/build .

#TODO remover trecho que sua aplicação nao trabalha com a pasta assets, para liberar arquivos para fora
COPY ./assets ./assets
RUN mkdir /app/tmpLock && chown -R node:node /app/tmpLock && chmod 755 /app/tmpLock && \
mkdir /app/_temp && chown -R node:node /app/_temp && chmod 755 /app/_temp
USER node

#TODO remover o trecho de 'node ace migration:run --force' caso a API em questão não tenho migrations para rodar
ENTRYPOINT sleep 5 ; if [ -e "/vault/secrets/.env" ] ; then source /vault/secrets/.env ; fi ; node ace migration:run --force && yarn start:onContainer
