This is a [Next.js](https://nextjs.org) project bootstrapped with [`create-next-app`](https://nextjs.org/docs/app/api-reference/cli/create-next-app).

## プロジェクトの起動！

First, run the development server:

```bash
npm run dev
# or
yarn dev
# or
pnpm dev
# or
bun dev
```

Open [http://localhost:3000](http://localhost:3000) with your browser to see the result.

You can start editing the page by modifying `app/page.tsx`. The page auto-updates as you edit the file.

This project uses [`next/font`](https://nextjs.org/docs/app/building-your-application/optimizing/fonts) to automatically optimize and load [Geist](https://vercel.com/font), a new font family for Vercel.

## 行った環境構築

CLOUDFLARE+NEXT.JS+PRISMA でデプロイするための手順です。

### Next.js のインストール(いったんバージョン 14)

```bash
npx create-next-app@14
```

### CLOUDFLARE でデプロイするための特有のライブラリをインストール

```bash
npm install @opennextjs/cloudflare wrangler
```

package.json の scripts に以下のコマンドを追加

```json:package.json
"scripts": {
    // "dev"や"build"...
    "preview": "opennextjs-cloudflare build && opennextjs-cloudflare preview",
    "deploy": "opennextjs-cloudflare build && opennextjs-cloudflare deploy",
    "cf-typegen": "wrangler types --env-interface CloudflareEnv cloudflare-env.d.ts"
},
```

ルートディレクトリに open-next.config.ts を作成

```typescript:open-next.config.ts
import { defineCloudflareConfig } from "@opennextjs/cloudflare";

export default defineCloudflareConfig();
```

ルートディレクトリに wrangler.jsonc を作成

```jsonc:wrangler.jsonc
{
  "main": ".open-next/worker.js",
  "name": "cf-nextjs-prisma", //ここだけいい感じのプロジェクト名に変更
  "compatibility_date": "2025-03-25",
  "compatibility_flags": ["nodejs_compat"],
  "assets": {
    "directory": ".open-next/assets",
    "binding": "ASSETS"
  }
}

```

### CLOUDFLARE にデプロイ

ここまで来たら GITHUB にプッシュ。

CLOUDFLARE 管理画面＞コンピューティング＞ Workers＆Pages ＞作成＞ Import a repository

で読み込みたいレポジトリを指定

名前を記入し、ビルドコマンドとデプロイコマンドを以下のように設定

```bash:ビルドコマンド
npm run build
```

```bash:デプロイコマンド
npm run deploy
```
