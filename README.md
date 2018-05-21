# yii2-docker
Docker env for yii2 projects
#Не забудьте добавить в ваши хосты из env в /etc/hosts
PMA открывается по localhost:портуказаный в env
## Установка
Установите Docker с официального сайта, если он еще не установлен
  https://www.docker.com/get-docker
Для убунту устатновите docker-compose помимо docker 
```
$ sudo curl -L https://github.com/docker/compose/releases/download/1.17.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose

$ sudo chmod +x /usr/local/bin/docker-compose

$ docker-compose --version
docker-compose version 1.17.0, build 1719ceb
```
  
Клонируйте этот репозиторий в свой проект

```$ git clone https://github.com/wannabelinus/yii2-docker.git ```

добавьте его в .gitignore
```
gitignore file:
...
%name_of_cloned_folder%
...
```
Отредактируйте docker-compose.yml на ваше усмотрение 
```
data:
  image: busybox:latest
  volumes:
    - ../:/app
  entrypoint: tail -f /dev/null

app:
  build: docker/php
  working_dir: /app
  volumes_from:
    - data
  expose:
    - 9000
  links:
    - db
    - mailcatcher
  environment:
    XDEBUG_CONFIG: "idekey=PHPSTORM remote_enable=On remote_connect_back=On"

nginx:
  image: nginx:1.12-alpine
  ports:
    - "8000:8000"
  volumes:
    - ./:/app
    - ./docker/nginx/vhost.conf:/etc/nginx/conf.d/vhost.conf
  links:
    - app

mailcatcher:
    image: schickling/mailcatcher:latest
    ports:
      - "1080:1080"

db:
  image: mysql:5.7
  volumes:
    - /var/lib/mysql
  ports:
    - "3307:3307"
  environment:
    MYSQL_ROOT_PASSWORD: root
    MYSQL_DATABASE: yii2
    MYSQL_USER: dbu
    MYSQL_PASSWORD: pass

phpmyadmin:
  image: phpmyadmin/phpmyadmin
  links:
    - db
  environment:
     MYSQL_USERNAME: root;
     MYSQL_ROOT_PASSWORD: root
     PMA_HOST: db
  ports:
    - '8001:80'    

```

Из папки этого репозитория:

```$ docker-compose build```

```$ docker-compose up -d```

Чтобы остановить: 

```$ docker-compose down ```

Переход в командную строку включеного приложения:

``` $ docker-compose exec app bash ```

Файлы настроек: 

PHP
/docker/php

Nginx 
/docker/nginx

## Конфигурация БД (config/db.php)
```
<?php
return [
    'class' => 'yii\db\Connection',
    'dsn' => 'mysql:host=db;dbname=yii2',
    'username' => 'dbu',
    'password' => 'pass',
    'charset' => 'utf8',

    // Schema cache options (for production environment)
    //'enableSchemaCache' => true,
    //'schemaCacheDuration' => 60,
    //'schemaCache' => 'cache',
];
```
