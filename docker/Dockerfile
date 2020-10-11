# Dockerfile для веб-игры из следующего репозитория: https://github.com/BassinD/NIR-utils/tree/master/nodejs_game
FROM mhart/alpine-node:14

# создание директории приложения
WORKDIR /usr/src/app

# установка зависимостей
# символ астериск ("*") используется для того чтобы по возможности 
# скопировать оба файла: package.json и package-lock.json
COPY ./nodejs_game/package*.json ./

RUN npm ci --only=production

# копируем исходный код
COPY ./nodejs_game .

EXPOSE 8080
CMD [ "npm", "run", "start" ]
