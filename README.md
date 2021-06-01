# ProjectPayment for Express42
## _Тестовое задание_



[![Build Status](https://travis-ci.com/EmilMind/PaymentProject.svg?branch=main)](https://travis-ci.com/github/EmilMind/PaymentProject)

За основу в данном задании был взят проект, имитирующий добавление и хранения банковских карт. Взят с открытого репозитория [GitHub][df1].
В главных ролях:
- Angular 11
- Asp.Net Core 5.0 Web API CRUD
- Nginx
- PostgreSQL 13
- Docker-compose
- ✨Magic ✨
- p.s. Дополнительно произвел сборку образа и деплой в [GitLab][df2]. Результат доступен по адресу: http://lizzardwizzard.space 

## Коротко о создании Docker контейнра

> В данной части блога я хотел бы рассказать вам,
> как запустить приложение Angular в контейнере Docker.
> Контейнер — это среда в которой выполняется код.
> Поэтому ее необходимо предварительно настроить
> и адаптировать для запуска и работы приложения.


Процесс создания контейнера можно разделить на 3 основных этапа: сборка проекта, конфигурация образа и его публикация в виде контейнера.
Для сборки и создания образа используется Dockerfile:

```dockerfile
FROM node:12.16.3-alpine As builder
WORKDIR /usr/src/app
COPY package.json package-lock.json ./
RUN npm install
COPY . .
RUN npm run build --prod

FROM nginx:1.19.10-alpine
COPY --from=builder /usr/src/app/dist/PaymentApp/ /usr/share/nginx/html
```

- FROM: получение базового образа сборки проекта и наименование этапа компиляции как builder
- WORKDIR: установка рабочего каталога по умолчанию
- COPY: копирование файлов package.json и package-lock.json из локального корневого каталога. Этот файл содержит все зависимости для приложения
- RUN: установка необходимых библиотек (на основе файла, с предыдущего шага)
- COPY: копирование всех оставшихся файлов с кодом
- RUN: компиляция приложения
- FROM: получение образа для запуска приложения (веб-сервер)
- COPY: копирование скомпилированного проекта с этапа builder в веб-сервер

Конфигурация реализуется с помощью файла docker-compose.yml 
Данный файл позволяет описать все необходимое окружения для работы данного приложения (проброс портов, подключение вольюмов и локальных сетей, зависимости запуска от других контейнеров).
- Процесс сборки образа по Dockerfile выполняется с помощью команды: _docker-compose -f docker-compose.yml build_
- Загрузка данного образа в репозиторий Docker Hub для его хранения: _docker-compose -f docker-compose.yml push_
- Запуск контейнеров, описанных в данном файле: _docker-compose -f docker-compose.yml up_
 
```docker-compose.yml
version: '3.8'
services:
    angular:
        container_name: Payment.Angular
        image: emilmind/deep-dark-repo:travis_ANGULAR
        build: ./PaymentApp
        pull_policy: always
        ports: 
            - "4321:80"
        depends_on:
            - api
        networks:
            - Payment.LocalNetwork
networks:
    Payment.LocalNetwork:
        driver: bridge
        name: Payment.LocalNetwork
```


[//]: 

   [df1]: <https://github.com/CodAffection/Asp.Net-Core-5.0-Web-API-CRUD-with-Angular-11>
   [df2]: <https://gitlab.com/emilmind/paymentproject>

