# docker 環境構築

docker フォルダを作成し、その下に docker-compose.yml を作成する

```yml
services:
  backend:
    container_name: backend
    image: node:20
    working_dir: /app
    tty: true
    depends_on:
      - db
    ports:
      - 7001:7001
    volumes:
      - ../:/app
    networks:
      - backend

  db:
    container_name: db
    image: mysql:8
    restart: always
    ports:
      - 3306:3306
    environment:
      - MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD}
      - MYSQL_HOST=${MYSQL_HOST}
      - MYSQL_USER=${MYSQL_USER}
      - MYSQL_PASSWORD=${MYSQL_PASSWORD}
      - MYSQL_DATABASE=${MYSQL_DATABASE}
    volumes:
      - ./db/conf.d:/etc/mysql/conf.d
      - ./db/data:/var/lib/mysql
    networks:
      - backend

volumes:
  mysql:

networks:
  backend:
```

docker-compose.yml と同じ場所に.env を作成する

```sh
MYSQL_ROOT_PASSWORD=password
MYSQL_HOST=localhost
MYSQL_USER=test
MYSQL_PASSWORD=password
MYSQL_DATABASE=test_database
```

docker/db/conf.d/に my.cnf を作成する

```sh
[mysqld]
default-time-zone = 'Asia/Tokyo'
character-set-server = utf8mb4
collation-server = utf8mb4_bin

[client]
default-character-set = utf8mb4
```

docker の起動コマンド

```sh
docker compose up -d
```

backend と db の疎通確認は、backend のコンテナに入って DB 接続を行う

```sh
apt-get update
apt-get install -y default-mysql-client
mysql -h db -u test -ppassword test_database
```
