# Изолированный сервер в контенере Docker
Контейнер для быстрого разворачивания сервера в контенере Docker
Помимо сервера включены дополнительные часто используемые сервисы
В сборку включены:
  + Php 7.4.1
  + Nginx
  + Mysql 5.7
  + Jenkins ( Отключена по умолчанию )
  + Jira ( Отключена по умолчанию )
  + Phpmyadmin
# Быстрый старт
Для начала работы необходимо установить пакеты [docker](https://docs.docker.com/install/linux/docker-ce/) и [docker-compose](https://docs.docker.com/compose/install/), подробнее об установке описано на сайте разработчика. 
Для клонирования репозитория необходимо выполнить: 
```sh
git clone git@github.com:scagroup/web-server.git
``` 
Для сборки контенеров
```sh 
docker-compose build 
``` 
и для запуска контенера 
```sh 
docker-compose up -d 
```
# Конфигурирования сервисов
В сборку включены дополнительные сервисы, помимо веб сервера. Для конфигурации сервисов необходимо отредактировать файл ```docker-compose.yaml ```.
``` yaml
services:
  #php: 
  #  build: 
  #    context: ./docker_config
  #    dockerfile: php
  #  links: 
  #    -  mysql
  #  ports:
  #    -  9000:9000
  #  container_name: "php"
  #  volumes: 
  #    -  ./data/www/:/var/www/html/
  nginx:
    build: 
      context: ./docker_config
      dockerfile: nginx
    container_name: "nginx"
    ports:
      -  80:80
    links: 
      -  mysql
      -  php
    volumes: 
      - ./config/nginx.conf:/etc/nginx/conf.d/default.conf
      - ./data/www/:/var/www/html/
```
Сервисы котрые вы не планируете использовать, можно отключить, путем комментирования блока кода сервиса, как показано. В примере мы отключаем сервис php, но так делать не нежно потому что сервис nginx зависит от контейнера php.
После настройки сборки, необходимо выполнить комманды: 
Для сборки контенеров
```sh 
docker-compose build 
``` 
и для запуска контенера 
```sh 
docker-compose up -d 
```
# Конфигурация сборок конткнеров
Для большей вариативности, сборка всех образов, вынесена в отдельные файлы в директории ```docker_config```. Вы можете сконфигурировать любой образ путем изменения конфигурации файла сборки докер крнтейнера. 
```sh
│           
├── docker-compose.yaml
├── docker_config
│   ├── jenkins
│   ├── jira
│   ├── mysql
│   ├── nginx
│   ├── php
│   └── pma
└── README.md
```

```dockerfile
FROM jenkins/jenkins:latest

USER root

RUN apt-get update
```
В данном примере показано, как мы выгрузили реестр из репозитория пакетного менеджера командой `RUN apt-get update`
# Конфигурирование Веб-сервера.
Конфигурация сервера хранится в файле `nginx.conf`. Для конфигурации используется синтаксис [nginx](https://nginx.org/ru/), c которым вы можете ознакомиться на сайте разработчика.
```sh
│           
├── docker-compose.yaml
├── config
│   └── nginx.conf
└── README.md
```
В текущей версии сервер конфигурируется для домена `localhost`. Если есть необходимость добавить дополнительные домены и хранить конфигурации в разных файлах, необходимо в файле `docker-compose.yaml` в разделе сервиса Nginx в подключаемый раздел подключать целиком папку с конфигурациями. Появится дополниельная проблема связанная с перенаправлением хостов. Необходимо будет дополнительно конфигурировать файл `/etc/hosts` в контейнере nginx, например на этапе сборки контейнера.
# Конфигурация версий сервисов
Для удобства и быстроты сборки контейнера с необходимыми версиями пакетов, докер контейнерные файлы параметризованы через файл `.env`.
```sh
├── docker-compose.yaml
├── docker_config
├── config
├── .enf
└── README.md
```
Файл выглядит следующим образом: 
```js
FOLDER_CONFIG_DOCKER=docker_config
...
JIRA_PORT=8002

PHP_VERSION=7.4
NGINX_VERSION=stable
MYSQL_VERSION=5.7
PMA_VERSION=latest
JENKINS_VERSION=latest
JIRA_VERSION=latest
```
Указывая версию не забываем пересобрать контейнер
``` 
docker-compose build 
``` 
и для запуска контенера 
``` 
docker-compose up -d 
```
