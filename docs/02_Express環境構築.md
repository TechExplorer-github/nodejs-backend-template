# Express 環境構築

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
