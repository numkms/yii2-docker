# yii2-docker
Docker env for yii2 projects
#Не забудьте добавить в ваши хосты из env в /etc/hosts
PMA открывается по localhost:портуказаный в env
## Установка
Установите Docker с официального сайта, если он еще не установлен
  https://www.docker.com/get-docker
  
Клонируйте этот репозиторий в свой проект и добавьте его в .gitignore

```$ git clone https://github.com/wannabelinus/yii2-docker.git ```

Отредактируйте .env под свой проект

```
# Framework
# ---------
YII_DEBUG   = true
YII_ENV     = dev

# Application
# -----------
LINK_ASSETS=true

# Databases
# ---------
DB_DSN           = mysql:host=db;port=3306;dbname=yii2
DB_USERNAME      = dbu
DB_PASSWORD      = pass
DB_TABLE_PREFIX  =

TEST_DB_DSN           = mysql:host=localhost;port=3306;dbname=yii2_test
TEST_DB_USERNAME      = root
TEST_DB_PASSWORD      = root

# Urls
# ----
FRONTEND_HOST_INFO    = http://docker.dev
BACKEND_HOST_INFO     = http://backend.docker.dev
STORAGE_HOST_INFO     = http://storage.docker.dev

# Single domain example
# ----
#FRONTEND_HOST_INFO    = http://docker.dev
#FRONTEND_BASE_URL     = /
#BACKEND_HOST_INFO     = http://docker.dev
#BACKEND_BASE_URL      = /backend/web
#STORAGE_HOST_INFO     = http://docker.dev
#STORAGE_HOST_INFO     = /storage/web


# Other
# -----
SMTP_HOST = mailcatcher
SMTP_PORT = 1025

FRONTEND_COOKIE_VALIDATION_KEY = pHgmE5O8O1yqaHWAn-CfU9VurkuYfJTd
BACKEND_COOKIE_VALIDATION_KEY = MZsjJJdrGbHGJvxff7KEchd10hecIiXV

ADMIN_EMAIL    = admin@docker.dev
ROBOT_EMAIL    = robot@docker.dev

GITHUB_CLIENT_ID = your-client-id
GITHUB_CLIENT_SECRET = your-client-secret

GLIDE_SIGN_KEY = GtFHHWa09D4jOWB8QyLTCKlZSvdFEqXm
GLIDE_MAX_IMAGE_SIZE = 4000000
````

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



