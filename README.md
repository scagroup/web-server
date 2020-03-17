# Изолированный сервер в контенере Docker
Контейнер для быстрого разворачивания сервера в контенере Docker
Помимо сервера включены дополнительные часто используемые сервисы
В сборку включены:
  + Php 7.4.1
  + Nginx
  + Mysql 5.7
  + Jenkins
  + Jira
# Быстрый старт
Для начала работы необходимо установить пакеты [docker](https://docs.docker.com/install/linux/docker-ce/) и [docker-compose](https://docs.docker.com/compose/install/), подробнее об установке описано на сайте разработчика. 
Для клонирования репозитория необходимо выполнить: 
```git clone git@github.com:scagroup/web-server.git```
Перед начало запуска необходимо определиться с используемыми сервисами и отредактировать файл docker-compose.yaml.
```
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
   Для сборки контенеров` docker-compose build` и для запуска контенера `docker-compose up -d`