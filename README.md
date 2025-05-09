
![github workflow for taski](https://github.com/Tumanova-Alina/taski-docker/actions/workflows/main.yml/badge.svg?event=push)


# Taski - сервис для планирования задач.
 
 
### Описание проекта: 
________________________________________________________________________________________________________
Taski - проект для занятых людей. Зарегистрированные пользователи могут записывать свои текущие планы и задачи, которые они хотели бы выполнить в ближайшее время. Есть возможность просматривать все свои дела и их статус выполнения, отмечать реализованные пункты. 

### Технологии:
________________________________________________________________________________________________________
- Docker
- PostgreSQL
- Python 3.9
- Node.js
- Git 
- Nginx 
- Gunicorn 
- Django (backend) 
- React (frontend)
- Github Actions
 
### Запуск проекта: 
________________________________________________________________________________________________________
 - Клонирование репозитория:
 
    ```bash
    https://github.com/Tumanova-Alina/taski-docker.git
    ```
    ```bash
    cd taski-docker
    ```

 - Создание файла .env:

    ```bash
   POSTGRES_USER=логин_от_бд
   POSTGRES_PASSWORD=пароль_от_бд
   POSTGRES_DB=название_бд
   DB_HOST=название_хоста
   DB_PORT=5432
   SECRET_KEY=django_settings_secret_key
   ALLOWED_HOSTS=127.0.0.1, localhost
    ```

 - Создание repository secrets в GitHub Actions:

    > [!TIP]
    > Репозиторий проекта -> settings -> secrets and variables -> actions -> new repository secret

    ```
    DOCKER_USERNAME=логин в Docker Hub
    DOCKER_PASSWORD=пароль для Docker Hub
    SSH_KEY=закрытый SSH-ключ для доступа к продакшен-серверу
    SSH_PASSPHRASE=passphrase для этого ключа
    USER=username для доступа к продакшен-серверу
    HOST=адрес хоста для доступа к продакшен-серверу
    TELEGRAM_TO=ID своего телеграм-аккаунта
    TELEGRAM_TOKEN=токен telegram-бота
    ``` 

### Создание Docker-образов:
________________________________________________________________________________________________________
 - Замена username на ваш логин на DockerHub:

    ```bash
    cd frontend
    docker build -t username/taski_frontend .
    cd ../backend
    docker build -t username/taski_backend .
    cd ../nginx
    docker build -t username/taski_gateway . 
    ```

 - Загрузка образов на DockerHub:

    ```bash
    docker push username/taski_frontend
    docker push username/taski_backend
    docker push username/taski_gateway
    ```
  
### Деплой на удалённый сервер:
________________________________________________________________________________________________________
 - Подключение к удаленному серверу:

    ```bash
    ssh -i путь_до_файла_с_SSH_ключом/название_файла_с_SSH_ключом имя_пользователя@ip_адрес_сервера 
    ```

 - Создание на сервере директории kittygram через терминал:

    ```bash
    mkdir taski
    ```

 - Установка docker compose на сервер:

    ```bash
    sudo apt update
    sudo apt install curl
    curl -fSL https://get.docker.com -o get-docker.sh
    sudo sh ./get-docker.sh
    sudo apt-get install docker-compose-plugin
    ```

 - Копирование файлов docker-compose.production.yml и .env в директорию kittygram/ :

    ```bash
    scp -i path_to_SSH/SSH_name docker-compose.production.yml username@server_ip:/home/username/taski/docker-compose.production.yml
    ```

 - Запуск docker compose в режиме демона:

    ```bash
    sudo docker compose -f docker-compose.production.yml up -d
    ```

 - Выполнение миграций, сбор статики бэкенда и копирование их в /backend_static/static/:

    ```bash
    sudo docker compose -f docker-compose.production.yml exec backend python manage.py migrate
    sudo docker compose -f docker-compose.production.yml exec backend python manage.py collectstatic
    sudo docker compose -f docker-compose.production.yml exec backend cp -r /app/collected_static/. /backend_static/static/
    ```

 - На сервере открытие в редакторе nano конфига nginx:

    ```bash
    sudo nano /etc/nginx/sites-enabled/default
   
    ```

 - Добавление настройки location в секции server:

    ```bash
    location / {
        proxy_set_header Host $http_host;
        proxy_pass http://127.0.0.1:9000;
    }
    ```

 - Проверка работоспособности конфигураций и перезапуск Nginx:

    ```bash
    sudo nginx -t 
    sudo service nginx reload
    ```


________________________________________________________________________________________________________

## Автор:
+ **Алина Туманова** [Tumanova-Alina](https://github.com/Tumanova-Alina)