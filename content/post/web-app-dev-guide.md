---
date: '2026-03-02'
draft: false
title: 'Webアプリ開発リポジトリの作り方・使い方ガイド'
description: 'Vite + React + TypeScript で Web アプリ開発用リポジトリを作成し、GitHub Pages にデプロイするまでの手順を、web_app_test を実例に解説します。'
tags: ['Web開発', 'React', 'Vite', 'TypeScript', 'GitHub Pages']
categories: ['Tech']
ShowToc: true
TocOpen: true
---

## はじめに

個人プロジェクトやちょっとした Web アプリのアイデアを試したいとき、
**テンプレートからリポジトリを一発で作成** → **ローカルで開発** → **GitHub Pages に自動デプロイ**
というワークフローがあると非常に便利です。

この記事では、実際に作成した [**web_app_test**](https://github.com/Key-2014/web_app_test) リポジトリ（関数電卓アプリ）を例に、
Web アプリ開発リポジトリの作り方から GitHub Pages へのデプロイまでを一通り解説します。

---

## 1. 技術スタック概要

| カテゴリ             | ツール / ライブラリ                                  |
| -------------------- | ---------------------------------------------------- |
| ビルドツール         | [Vite](https://vite.dev/)                            |
| UI ライブラリ        | [React](https://react.dev/)                          |
| 言語                 | [TypeScript](https://www.typescriptlang.org/)        |
| CSS フレームワーク   | [Tailwind CSS v4](https://tailwindcss.com/)          |
| パッケージマネージャ | [pnpm](https://pnpm.io/)                             |
| CI/CD                | [GitHub Actions](https://docs.github.com/ja/actions) |
| ホスティング         | [GitHub Pages](https://pages.github.com/)            |

---

## 2. リポジトリの新規作成

### 2.1 Vite でプロジェクトを初期化する

まず、`create-vite` を使って React + TypeScript のテンプレートを生成します。

```bash
# pnpm がインストールされていない場合
npm install -g pnpm

# プロジェクトを作成（例: my_web_app という名前）
pnpm create vite my_web_app --template react-ts
```

これだけで React + TypeScript の開発環境が整います。

### 2.2 GitHub リポジトリの作成

1. [GitHub](https://github.com) で新しいリポジトリを作成（例: `my_web_app`）
2. ローカルリポジトリを初期化してプッシュ：

```bash
cd my_web_app
git init
git add .
git commit -m "Initial commit"
git remote add origin https://github.com/<ユーザー名>/my_web_app.git
git push -u origin main
```

---

## 3. ディレクトリ構成

Vite + React + TypeScript テンプレートで生成されるディレクトリ構成は以下の通りです：

```
my_web_app/
├── .github/
│   └── workflows/
│       └── deploy.yml          # GitHub Pages デプロイ用ワークフロー
├── public/                     # 静的アセット（favicon 等）
├── src/
│   ├── assets/                 # 画像等のアセット
│   ├── App.tsx                 # メインの React コンポーネント
│   ├── main.tsx                # エントリーポイント
│   └── index.css               # グローバル CSS
├── index.html                  # HTML テンプレート
├── package.json                # 依存関係・スクリプト定義
├── tsconfig.json               # TypeScript 設定（ルート）
├── tsconfig.app.json           # TypeScript 設定（アプリ用）
├── tsconfig.node.json          # TypeScript 設定（Node 用）
├── vite.config.ts              # Vite 設定
├── eslint.config.js            # ESLint 設定
└── .gitignore                  # Git 無視ファイル
```

### 主要ファイルの役割

- **`src/App.tsx`** — アプリの UI を定義するメインコンポーネント
- **`src/main.tsx`** — React アプリを DOM にマウントするエントリーポイント
- **`vite.config.ts`** — Vite のビルド設定（プラグイン・ベースパス等）
- **`index.html`** — SPA のベースとなる HTML。`<div id="root">` に React がマウントされる

---

## 4. Tailwind CSS の導入

Tailwind CSS v4 では、Vite プラグインとして導入できます。

### 4.1 インストール

```bash
pnpm add tailwindcss @tailwindcss/vite
```

### 4.2 Vite 設定の更新

`vite.config.ts` に Tailwind プラグインを追加します：

```ts
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react-swc';
import tailwindcss from '@tailwindcss/vite';

export default defineConfig({
  plugins: [react(), tailwindcss()],
  base: '/my_web_app/',  // GitHub Pages 用のベースパス
});
```

### 4.3 CSS でインポート

`src/index.css` に以下を記述します：

```css
@import "tailwindcss";
```

これだけで Tailwind CSS が使えるようになります。`className` に Tailwind のユーティリティクラスを書くだけで、スタイルが適用されます。

---

## 5. ローカル開発

### 5.1 依存関係のインストール

```bash
pnpm install
```

### 5.2 開発サーバーの起動

```bash
pnpm dev
```

ブラウザで `http://localhost:5173/my_web_app/` にアクセスすると、ホットリロード対応の開発サーバーが立ち上がります。ソースコードを保存するたびに、ブラウザが自動で更新されます。

### 5.3 その他のコマンド

| コマンド       | 説明                                         |
| -------------- | -------------------------------------------- |
| `pnpm dev`     | 開発サーバーを起動                           |
| `pnpm build`   | プロダクションビルドを実行（`dist/` に出力） |
| `pnpm preview` | ビルド済みアプリをローカルでプレビュー       |
| `pnpm lint`    | ESLint でコードを静的解析                    |

---

## 6. GitHub Pages へのデプロイ

GitHub Actions を使って、`main` ブランチへの push 時に自動でビルド＆デプロイする設定を行います。

### 6.1 `vite.config.ts` の `base` 設定

GitHub Pages ではリポジトリ名がサブパスになるため、`base` を設定しておく必要があります：

```ts
export default defineConfig({
  plugins: [react(), tailwindcss()],
  base: '/my_web_app/',  // リポジトリ名に合わせる
});
```

### 6.2 GitHub Actions ワークフローの作成

`.github/workflows/deploy.yml` を作成します：

```yaml
name: Deploy static content to Pages

on:
  push:
    branches: ['main']
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: 'pages'
  cancel-in-progress: true

jobs:
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v5
      - name: Setup pnpm
        uses: pnpm/action-setup@v4
        with:
          version: latest
      - name: Set up Node
        uses: actions/setup-node@v6
        with:
          node-version: lts/*
          cache: 'pnpm'
      - name: Install dependencies
        run: pnpm install --frozen-lockfile
      - name: Build
        run: pnpm run build
      - name: Setup Pages
        uses: actions/configure-pages@v5
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v4
        with:
          path: './dist'
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

### 6.3 GitHub リポジトリの設定

1. リポジトリの **Settings → Pages** を開く
2. **Source** を **GitHub Actions** に設定する

これで、`main` ブランチに push するたびに自動的にビルド → デプロイが実行されます。

---

## 7. 実際の例：web_app_test（関数電卓アプリ）

上記の手順で作成したリポジトリの実例として、[**web_app_test**](https://github.com/Key-2014/web_app_test) を紹介します。

### 概要

- **アプリ内容**: Tailwind CSS v4 を使った関数電卓
- **機能**: 四則演算、三角関数（sin/cos/tan）、対数（ln/log）、平方根、指数計算、Degree/Radian 切替
- **デプロイ先**: [https://key-2014.github.io/web_app_test/](https://key-2014.github.io/web_app_test/)

### ポイント

- `App.tsx` にすべてのロジックと UI をまとめたシンプルな構成
- キーボード入力にも対応（数字キー、Enter で計算、Backspace で削除、Escape でクリア）
- `@vitejs/plugin-react-swc` を使って高速なビルドを実現

---

## まとめ

Webアプリ開発リポジトリを作成する手順をまとめると：

1. `pnpm create vite` でプロジェクトを初期化
2. 必要に応じて Tailwind CSS 等を導入
3. `pnpm dev` でローカル開発
4. `.github/workflows/deploy.yml` を配置
5. GitHub の Pages 設定で Source を GitHub Actions に変更
6. `main` ブランチに push すれば自動デプロイ！

このワークフローを使えば、新しい Web アプリのアイデアをサッと形にして公開できます。
ぜひ活用してみてください 🚀
