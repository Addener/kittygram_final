[![Main Kittygram workflow](https://github.com/Addener/kittygram_final/actions/workflows/main.yml/badge.svg?branch=main)](https://github.com/Addener/kittygram_final/actions/workflows/main.yml)

### Описание проекта 
Kittygram - сервис для любителей котиков.

Что умеет проект:

- Добавлять, просматривать, редактировать и удалять котиков.
- Добавлять новые и присваивать уже существующие достижения. 
- Просматривать чужих котов и их достижения.

### Стек технологий:
[![GitHub%20Actions](https://img.shields.io/badge/-GitHub%20Actions-464646?style=flat-square&logo=GitHub%20actions)](https://github.com/features/actions)
[![docker](https://img.shields.io/badge/-Docker-464646?style=flat-square&logo=docker)](https://www.docker.com)
[![Python](https://img.shields.io/badge/-Python-464646?style=flat-square&logo=Python)](https://www.python.org)
[![Django](https://img.shields.io/badge/-Django-464646?style=flat-square&logo=Django)](https://www.djangoproject.com/)
[![Django REST Framework](https://img.shields.io/badge/-Django%20REST%20Framework-464646?style=flat-square&logo=Django%20REST%20Framework)](https://www.django-rest-framework.org)
[![PostgreSQL](https://img.shields.io/badge/-PostgreSQL-464646?style=flat-square&logo=PostgreSQL)](https://www.postgresql.org)
[![gunicorn](https://img.shields.io/badge/-gunicorn-464646?style=flat-square&logo=gunicorn)](https://gunicorn.org)
[![Nginx](https://img.shields.io/badge/-NGINX-464646?style=flat-square&logo=NGINX)](https://nginx.org/ru)

## Установка 

1. Клонируйте репозиторий на свой компьютер:

    ```bash
    git clone https://github.com/Addener/kittygram_final.git
    ```
    ```bash
    cd kittygram
    ```
2. Создайте файл .env и заполните его своими данными. Перечень данных указан в корневой директории проекта в файле .env.example.


### Создание Docker-образов

1.  Замените username на ваш логин на DockerHub:

    ```bash
    cd frontend
    docker build -t username/kittygram_frontend .
    cd ../backend
    docker build -t username/kittygram_backend .
    cd ../nginx
    docker build -t username/kittygram_gateway . 
    ```

2. Загрузите образы на DockerHub:

    ```bash
    docker push username/kittygram_frontend
    docker push username/kittygram_backend
    docker push username/kittygram_gateway
    ```

### Как заполнить .env

    ```bash
    POSTGRES_DB=(Имя базы данных)
    POSTGRES_USER=(Имя Пользователя Базы)
    POSTGRES_PASSWORD=(Пароль к базе)
    DB_NAME=(Имя хоста)
    DB_HOST=(Адрес, по которому Django будет соединяться с БД)
    DB_PORT=(Порт соединения к БД)
    DEBUG=(Вкл/Выкл отладку(использовать True/False))
    ALLOWED_HOSTS =(Список разрешённых хостов, в таком виде: ALLOWED_HOSTS=51.250.24.131, 127.0.0.1, localhost, cute-kittygram.ddns.net)
    ```


### Деплой на сервере

1. Подключитесь к удаленному серверу

    ```bash
    ssh -i путь_до_файла_с_SSH_ключом/название_файла_с_SSH_ключом имя_пользователя@ip_адрес_сервера 
    ```

2. Создайте на сервере директорию kittygram через терминал

    ```bash
    mkdir kittygram
    ```

3. Установка docker compose на сервер:

    ```bash
    sudo apt update
    sudo apt install curl
    curl -fSL https://get.docker.com -o get-docker.sh
    sudo sh ./get-docker.sh
    sudo apt-get install docker-compose-plugin
    ```

4. В директорию kittygram/ скопируйте файлы docker-compose.production.yml и .env:

    ```bash
    scp -i path_to_SSH/SSH_name docker-compose.production.yml username@server_ip:/home/username/kittygram/docker-compose.production.yml
    * ath_to_SSH — путь к файлу с SSH-ключом;
    * SSH_name — имя файла с SSH-ключом (без расширения);
    * username — ваше имя пользователя на сервере;
    * server_ip — IP вашего сервера.
    ```

5. Запустите docker compose в режиме демона:

    ```bash
    sudo docker compose -f docker-compose.production.yml up -d
    ```

6. Выполните миграции, соберите статические файлы бэкенда и скопируйте их в /backend_static/static/:

    ```bash
    sudo docker compose -f docker-compose.production.yml exec backend python manage.py migrate
    sudo docker compose -f docker-compose.production.yml exec backend python manage.py collectstatic
    sudo docker compose -f docker-compose.production.yml exec backend cp -r /app/collected_static/. /backend_static/static/
    ```

7. На сервере в редакторе nano откройте конфиг Nginx:

    ```bash
    sudo nano /etc/nginx/sites-enabled/default
    ```

8. Измените настройки location в секции server:

    ```bash
    location / {
        proxy_set_header Host $http_host;
        proxy_pass http://127.0.0.1:9000;
    }
    ```

9. Проверьте работоспособность конфига Nginx:

    ```bash
    sudo nginx -t
    ```
    Если ответ в терминале такой, значит, ошибок нет:
    ```bash
    nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
    nginx: configuration file /etc/nginx/nginx.conf test is successful
    ```

10. Перезапускаем Nginx
    ```bash
    sudo service nginx reload
    ```

### Настройка CI/CD

1. Файл workflow уже написан. Он находится в директории

    ```bash
    kittygram/.github/workflows/main.yml
    ```

2. Для адаптации его на своем сервере добавьте секреты в GitHub Actions:

    ```bash
    DOCKER_USERNAME                # имя пользователя в DockerHub
    DOCKER_PASSWORD                # пароль пользователя в DockerHub
    HOST                           # ip_address сервера
    USER                           # имя пользователя
    SSH_KEY                        # приватный ssh-ключ (cat ~/.ssh/id_rsa)
    SSH_PASSPHRASE                 # кодовая фраза (пароль) для ssh-ключа

    TELEGRAM_TO                    # id телеграм-аккаунта (можно узнать у @userinfobot, команда /start)
    TELEGRAM_TOKEN                 # токен бота (получить токен можно у @BotFather, /token, имя бота)

### Автор работы

Алексей Ужва

Tg: @uzhvaaa