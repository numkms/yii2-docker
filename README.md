# yii2-docker
Docker env for yii2 projects
#Не забудьте добавить в ваши хосты из env в /etc/hosts
PMA открывается по localhost:портуказаный в env
## Установка
Установите Docker с официального сайта, если он еще не установлен
  https://www.docker.com/get-docker
  
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



