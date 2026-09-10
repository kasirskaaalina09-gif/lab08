## Домашнее задание

В репозитории приведен код web-приложения, которое сохраняет в БД введенную информацию о задаче - ее имя.

## Часть I. Docker

### 1. Добавьте в код Dockerfile, который позволит запустить web-приложение с исходным кодом в каталоге app/ через Docker.

Клонируем исходный репозиторий с заданием:

```sh

$ git clone https://github.com/tp-lessons/lab_docker.git

```

Проверяем содержимое проекта:

```sh

$ ls

```

Вывод:

```text

app  db  LICENSE  README.md

```

Перемещаемся в папку `app` и проверяем ее содержимое:

```sh

$ cd app

$ ls

```

Вывод:

```text

app.py  models.py  requirements.txt  templates

```

Создаем файл `Dockerfile`:

```sh

$ nano Dockerfile

```

В него добавляем следующий код:

```dockerfile

FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 5000

CMD ["python", "app.py"]

```

Проверяем установленную версию Docker:

```sh

$ docker --version

```

Вывод:

```text

Docker version 29.1.3, build 29.1.3-0ubuntu3~24.04.2

```

Также проверяем работу Docker с помощью тестового контейнера:

```sh

$ docker run hello-world

```

<details>

<summary>Посмотреть полный вывод команды</summary>

```text

Unable to find image 'hello-world:latest' locally

latest: Pulling from library/hello-world

4f55086f7dd0: Pull complete

d5e71e642bf5: Download complete

Digest: sha256:5dd0d3e6e255913fc30f90b9f2b1d359cc2cbdb48090cc4b65f1676e203243cc

Status: Downloaded newer image for hello-world:latest

Hello from Docker!

This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:

 1. The Docker client contacted the Docker daemon.

 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.

    (amd64)

 3. The Docker daemon created a new container from that image which runs the

    executable that produces the output you are currently reading.

 4. The Docker daemon streamed that output to the Docker client, which sent it

    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:

 $ docker run -it ubuntu bash

```

</details>

Собираем Docker-образ приложения:

```sh

$ docker build -t my-flask-app .

```

<details>

<summary>Посмотреть полный вывод команды</summary>

```text

DEPRECATED: The legacy builder is deprecated and will be removed in a future release.

            Install the buildx component to build images with BuildKit:

            https://docs.docker.com/go/buildx/

Sending build context to Docker daemon  7.168kB

Step 1/7 : FROM python:3.9-slim

3.9-slim: Pulling from library/python

ea56f685404a: Pulling fs layer

fc7443084902: Pulling fs layer

38513bd72563: Pulling fs layer

b3ec39b36ae8: Pulling fs layer

a5e5b1b19090: Download complete

ea56f685404a: Download complete

967a6079ce08: Download complete

b3ec39b36ae8: Download complete

fc7443084902: Download complete

38513bd72563: Pull complete

b3ec39b36ae8: Pull complete

ea56f685404a: Pull complete

fc7443084902: Pull complete

Digest: sha256:2d97f6910b16bd338d3060f261f53f144965f755599aab1acda1e13cf1731b1b

Status: Downloaded newer image for python:3.9-slim

 ---> 2d97f6910b16

Step 2/7 : WORKDIR /app

 ---> Running in ab7e0f94d159

 ---> Removed intermediate container ab7e0f94d159

 ---> 38e5e2a293d0

Step 3/7 : COPY requirements.txt .

 ---> 2a2d649f64e4

Step 4/7 : RUN pip install --no-cache-dir -r requirements.txt

 ---> Running in 793a18596deb

Collecting flask

  Downloading flask-3.1.3-py3-none-any.whl

Collecting mysql-connector-python

  Downloading mysql_connector_python-9.4.0-cp39-cp39-manylinux_2_28_x86_64.whl

Successfully installed blinker-1.9.0 click-8.1.8 flask-3.1.3 importlib-metadata-8.7.1 itsdangerous-2.2.0 jinja2-3.1.6 markupsafe-3.0.3 mysql-connector-python-9.4.0 werkzeug-3.1.8 zipp-3.23.1

WARNING: Running pip as the 'root' user can result in broken permissions and conflicting behaviour with the system package manager.

 ---> Removed intermediate container 793a18596deb

 ---> baf701ddbcc8

Step 5/7 :
был изучен Docker и работа с контейнерами.

Был создан Dockerfile для Flask-приложения, собран Docker-образ и выполнен запуск приложения в отдельном контейнере.

Также была настроена совместная работа web-приложения и базы данных MySQL с помощью Docker Compose.

В результате приложение успешно подключается к базе данных и корректно отображает сохраненные данные. C
OPY . .

 ---> 6f0400b7a054

Step 6/7 : EXPOSE 5000

 ---> Running in 9bffedbaba7b

 ---> Removed intermediate container 9bffedbaba7b

 ---> b991d31b30f7

Step 7/7 : CMD ["python", "app.py"]

 ---> Running in 83053877b500

 ---> Removed intermediate container 83053877b500

 ---> d74e8c371609

Successfully built d74e8c371609

Successfully tagged my-flask-app:latest

```

</details>

### 2. Выполните запуск контейнера с приложением.

Запускаем контейнер:

```sh

$ docker run -d --name my-running-app -p 5000:5000 my-flask-app

```

Вывод:

```text

fc0389638a58c9a09aa9206935cadbae0d9c776554eb916466fcedd48ae587eb

```

Проверяем, что контейнер успешно запущен:

```sh

$ docker ps

```

Вывод:

```text

CONTAINER ID   IMAGE          COMMAND           CREATED          STATUS          PORTS                                         NAMES

fc0389638a58   my-flask-app   "python app.py"   36 seconds ago   Up 36 seconds   0.0.0.0:5000->5000/tcp, [::]:5000->5000/tcp   my-running-app

```

### 3. Скопируйте из консоли в каталог /home/ контейнера файл README.md.

Создаем файл README.md:

```sh

$ echo "Это пример README" > README.md

```

Копируем его в контейнер:

```sh

$ docker cp README.md my-running-app:/home/

```

Вывод:

```text

Successfully copied 2.05kB to my-running-app:/home/

```

### 4. Подключитесь к терминалу контейнера с приложением в интерактивном режиме. Проверьте, что скопированный файл находится в нужном каталоге.

Подключаемся к контейнеру:

```sh

$ docker exec -it my-running-app bash

```

Проверяем содержимое каталога `/home/`:

```sh

root@fc0389638a58:/app# ls /home/

```

Вывод:

```text

README.md

```

### 5. Выйдите из интерактивного режима.

```sh

root@fc0389638a58:/app# exit

```

Вывод:

```text

exit

```

### 6. Остановите контейнер с приложением.

Останавливаем контейнер:

```sh

$ docker stop my-running-app

```

Вывод:

```text

my-running-app

```

Удаляем контейнер:

```sh

$ docker rm my-running-app

```

Вывод:

```text

my-running-app

```

## Часть II. Docker Compose

### 1. Создайте файл docker-compose.yml таким образом, чтобы совместно с описанным в части 1 контейнером работала база данных MySQL.

Возвращаемся в корневой каталог проекта.

В результате структура проекта выглядит следующим образом:

```sh

$ ls

```

Вывод:

```text

app  db  docker-compose.yml  LICENSE  README.md

```

Также используется файл `.env` с настройками базы данных:

```sh

$ ls -a

```

Вывод:

```text

.  ..  app  db  docker-compose.yml  .env  .git  LICENSE  README.md

```

Файл `.env` содержит следующие переменные:

```text

DB_HOST=db

DB_USER=appuser

DB_PASSWORD=apppassword

DB_NAME=appdb

DB_ROOT_PASSWORD=rootpassword

```

Создаем файл `docker-compose.yml`:

```sh

$ nano docker-compose.yml

```

Добавляем следующую конфигурацию:

```yaml

services:

  web:

    build: ./app

    ports:

      - "5000:5000"

    environment:

      DB_HOST: ${DB_HOST}

      DB_USER: ${DB_USER}

      DB_PASS: ${DB_PASSWORD}

      DB_NAME: ${DB_NAME}

    depends_on:

      db:

        condition: service_healthy

  db:

    image: mysql:8.0

    restart: always

    environment:

      MYSQL_ROOT_PASSWORD: ${DB_ROOT_PASSWORD}

      MYSQL_DATABASE: ${DB_NAME}

      MYSQL_USER: ${DB_USER}

      MYSQL_PASSWORD: ${DB_PASSWORD}

    ports:

      - "3306:3306"

    volumes:

      - db_data:/var/lib/mysql

      - ./db/init.sql:/docker-entrypoint-initdb.d/init.sql

    healthcheck:

      test: ["CMD", "mysqladmin", "ping", "-h", "localhost", "-u", "root", "-p${DB_ROOT_PASSWORD}"]

      interval: 10s

      timeout: 5s

      retries: 5

volumes:

  db_data:

```

Для запуска Docker Compose был установлен пакет `docker-compose-v2`, так как используемая версия Docker первоначально не поддерживала команду:

```sh

$ docker compose up --build

```

После установки Docker Compose запускаем приложение и базу данных:

```sh

$ docker compose up --build

```

<details>

<summary>Посмотреть полный вывод команды</summary>

```text

WARNING: This is a development server. Do not use it in a production deployment. Use a production W
AI Tools Directory - dealsbe.com
AI Tools Directory - dealsbe.com
dealsbe.com
SGI server instead.

web-1  |  * Running on all addresses (0.0.0.0)

web-1  |  * Running on http://127.0.0.1:5000

web-1  |  * Running on http://172.18.0.3:5000

web-1  | Press CTRL+C to quit

db-1   | 2026-09-09T18:29:50.517141Z 0 [System] [MY-010910] [Server] /usr/sbin/mysqld: Shutdown complete (mysqld 8.0.46)  MySQL Community Server - GPL.

db-1   | 2026-09-09 18:29:50+00:00 [Note] [Entrypoint]: Temporary server stopped

db-1   |

db-1   | 2026-09-09 18:29:50+00:00 [Note] [Entrypoint]: MySQL init process done. Ready for start up.

db-1   |

db-1   | 2026-09-09T18:29:50.893945Z 0 [Warning] [MY-011068] [Server] The syntax '--skip-host-cache' is deprecated and will be removed in a future release. Please use SET GLOBAL host_cache_size=0 instead.

db-1   | 2026-09-09T18:29:50.894878Z 0 [System] [MY-010116] [Server] /usr/sbin/mysqld (mysqld 8.0.46) starting as process 1

db-1   | 2026-09-09T18:29:50.902579Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.

db-1   | 2026-09-09T18:29:51.142781Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.

db-1   | 2026-09-09T18:29:51.319224Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.

db-1   | 2026-09-09T18:29:51.319294Z 0 [System] [MY-013602] [Server] Channel mysql_main configured to support TLS. Encrypted connections are now supported for this channel.

db-1   | 2026-09-09T18:29:51.326269Z 0 [Warning] [MY-011810] [Server] Insecure configuration for --pid-file: Location '/var/run/mysqld' in the path is accessible to all OS users. Consider choosing a different directory.

db-1   | 2026-09-09T18:29:51.353895Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Bind-address: '::' port: 33060, socket: /var/run/mysqld/mysqlx.sock

db-1   | 2026-09-09T18:29:51.353964Z 0 [System] [MY-010931] [Server] /usr/sbin/mysqld: ready for connections. Version: '8.0.46'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server - GPL.

```

</details>

### 2. Проверка подключения приложения к базе данных.

После запуска приложения при первом открытии страницы была обнаружена проблема с отображением русских символов:

```text

Список из Базы Данных

ÐŸÑ€Ð¸Ð¼ÐµÑ€ 1

ÐŸÑ€Ð¸Ð¼ÐµÑ€ 2

```

Для исправления проблемы была изменена работа с кодировкой в файле `models.py`.

Также в `requirements.txt` была добавлена библиотека:

```text

pymysql

```

В файле `models.py` используется кодировка `utf8mb4`:

```python

import os

import pymysql

class ItemModel:

    def __init__(self):

        self.config = {

            'host': os.getenv('DB_HOST'),

            'user': os.getenv('DB_USER'),

            'password': os.getenv('DB_PASS'),

            'database': os.getenv('DB_NAME'),

            'charset': 'utf8mb4',

            'cursorclass': pymysql.cursors.DictCursor

        }

    def get_all_items(self):

        try:

            conn = pymysql.connect(**self.config)

            cursor = conn.cursor()

            cursor.execute('SELECT name FROM items')

            items = cursor.fetchall()

            cursor.close()

            conn.close()

            fixed = []

            for item in items:

                name = item['name']

                if isinstance(name, str):

                    try:

                        name = name.encode('cp1252').decode('utf-8')

                    except (UnicodeEncodeError, UnicodeDecodeError):

                        pass

                fixed.append({'name': name})

            return fixed

        except Exception as e:

            print(f'Error: {e}')

            return []

```

После внесения изменений контейнер был перезапущен.

При повторной проверке приложение корректно отображает данные из базы:

```text

Список из Базы Данных

Пример 1

Пример 2

```

### 3. Проверка работы приложения через браузер.

Приложение было открыто по адресу:

```text

http://localhost:5000

```

После исправления кодировки данные из базы данных отображаются корректно.

В ходе работы был сделан снимок экрана работающего приложения.

## Вывод

В ходе выполнения лабораторной работы был изучен Docker и работа с контейнерами
