# template-express-ts-typeorm

## 環境構築手順

```sh
npm init -y
npm i express dotenv cors helmet

npm i -D typescript
npm i -D @types/node @types/express @types/dotenv @types/cors @types/helmet

npx tsc --init
```

.env ファイルを作成し、PORT を記載する

```sh
PORT=7000
```

エントリーポイント(src/index.ts)を作成する

```ts
import * as dotenv from "dotenv";
import express from "express";
import cors from "cors";
import helmet from "helmet";

/**
 * App Variables
 */
dotenv.config();

if (!process.env.PORT) {
  console.log(`Not Found PORT`);
  process.exit(1);
}

const PORT: number = parseInt(process.env.PORT as string, 10);

const app = express();

/**
 *  App Configuration
 */
app.use(helmet());
app.use(cors());
app.use(express.json());

/**
 * Server Activation
 */
app.listen(PORT, () => {
  console.log(`Listening on port ${PORT}`);
});
```

ts-node-dev のライブラリをインストールする(コンパイルなしで起動可能)

```sh
npm i -D ts-node-dev
```

package.json の scripts に下記を追加する

```json
  "scripts": {
    "dev": "ts-node-dev --respawn --pretty --transpile-only src/index.ts"
  },
```

下記のコマンドで起動する

```sh
npm run dev
```

## docker 上で動くように環境構築

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
      - 8000:8000
    volumes:
      - ../src:/app
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
apt-get install -y default-mysql-client
mysql -h db -u test -ppassword test_database
```
