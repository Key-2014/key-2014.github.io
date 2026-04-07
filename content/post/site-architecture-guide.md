---
date: '2026-04-07'
draft: false
title: 'Key-2014.github.io の作り方と仕組み'
description: 'Hugo + PaperMod テーマ + GitHub Pages を使ったこのサイトの構成、ビルドフロー、デプロイの仕組みを解説します。'
tags: ['Hugo', 'PaperMod', 'GitHub Pages', 'GitHub Actions', 'サイト構築']
categories: ['Tech']
ShowToc: true
TocOpen: true
---

## はじめに

このページでは、**Key-2014.github.io**（このサイト）がどのように作られているかを解説します。

利用している技術スタック：

| カテゴリ | ツール |
| --- | --- |
| 静的サイトジェネレータ | [Hugo](https://gohugo.io/) |
| テーマ | [PaperMod](https://github.com/adityatelange/hugo-PaperMod) |
| ホスティング | [GitHub Pages](https://pages.github.com/) |
| CI/CD | [GitHub Actions](https://docs.github.com/ja/actions) |
| テーマ管理 | Git サブモジュール |

---

## Hugo とは？

[Hugo](https://gohugo.io/) は **Go 言語**で書かれた静的サイトジェネレータです。Markdown ファイルで記事を書くだけで、HTML/CSS/JS の静的サイトが自動生成されます。

### Hugo を選んだ理由

- ⚡ **ビルドが非常に高速**（数百ページでも数秒）
- 📝 **Markdown で記事が書ける**（`.md` ファイルを置くだけ）
- 🎨 **豊富なテーマ**が公開されている
- 🔧 **設定が YAML/TOML で簡潔**
- 🆓 **無料**、オープンソース

---

## PaperMod テーマ

このサイトで使用しているテーマは [**PaperMod**](https://github.com/adityatelange/hugo-PaperMod) です。

### PaperMod の特徴

- 🌗 **ダーク/ライトモード自動切替** — OS のテーマに追従
- 📖 **目次（Table of Contents）**の自動生成
- 🔍 **全文検索**に対応
- 📐 **レスポンシブデザイン** — PC でもスマホでも読みやすい
- 🧭 **パンくずリスト**（Breadcrumbs）表示
- 📋 **コードブロックのコピーボタン**

### テーマの導入方法

PaperMod は **Git サブモジュール** として管理しています：

```
[submodule "themes/PaperMod"]
  path = themes/PaperMod
  url = https://github.com/adityatelange/hugo-PaperMod.git
```

これにより、テーマの更新も `git submodule update --remote` 一発で行えます。

---

## サイトの設定（`hugo.yaml`）

サイト全体の設定は `hugo.yaml` に記述されています：

```yaml
baseURL: https://Key-2014.github.io/
title: Key's Sandbox
theme: PaperMod
languageCode: en-us
enableEmoji: true
mainsections: ["post"]
```

### メニュー設定

ナビゲーションメニューは `menu.main` で定義します：

```yaml
menu:
  main:
    - name: "About Me"
      url: /aboutme
      weight: 1
    - name: "Posts"
      url: /post/
      weight: 3
```

`weight` が小さいメニューほど左（先頭）に表示されます。

### パラメータ設定

PaperMod テーマ固有の設定は `params` セクションに記述します：

```yaml
params:
  env: production                # SEO メタタグを有効化
  description: "Writing about tech articles and daily events."
  author: "Key-2014"
  defaultTheme: "auto"           # OS に合わせて自動切替
  ShowPostNavLinks: true          # 前後の記事リンクを表示
  ShowBreadCrumbs: true           # パンくずリストを表示
  ShowCodeCopyButtons: true       # コードブロックにコピーボタン
  ShowToc: true                   # 目次を表示
```

---

## リポジトリの構成

```
Key-2014.github.io/
├── .github/
│   └── workflows/
│       └── hugo.yaml            # GitHub Actions デプロイ設定
├── archetypes/
│   └── default.md               # 新規記事のテンプレート
├── assets/                      # SCSS や JS などのアセット
├── content/
│   └── post/                    # 記事を置くディレクトリ
│       ├── site-architecture-guide.md
│       ├── typst-template-guide.md
│       └── web-app-dev-guide.md
├── data/                        # データファイル
├── i18n/                        # 多言語対応ファイル
├── layouts/                     # カスタムレイアウト
├── public/                      # ビルド出力（git 管理外）
├── static/                      # 静的ファイル（画像等）
├── themes/
│   └── PaperMod/                # テーマ（Git サブモジュール）
├── hugo.yaml                    # サイト設定
├── .gitmodules                  # サブモジュール定義
└── .gitignore
```

### 主なディレクトリの役割

| ディレクトリ | 役割 |
| --- | --- |
| `content/post/` | 記事の Markdown ファイルを置く場所 |
| `static/` | 画像やファイルなど、そのまま公開される静的ファイル |
| `layouts/` | テーマのレイアウトを上書きするカスタムテンプレート |
| `archetypes/` | `hugo new` で新規記事を作成する際のテンプレート |
| `themes/PaperMod/` | PaperMod テーマ本体（サブモジュール） |
| `public/` | `hugo` コマンドで生成されるビルド出力 |

---

## ビルド & デプロイの仕組み

### 全体フロー

```
Markdown を書く
     ↓
git commit & push (main ブランチ)
     ↓
GitHub Actions が自動起動
     ↓
Hugo がサイトをビルド
     ↓
GitHub Pages にデプロイ
     ↓
https://Key-2014.github.io/ に反映 🎉
```

### GitHub Actions ワークフロー

`.github/workflows/hugo.yaml` で定義されたワークフローが、`main` ブランチへの push 時に自動実行されます：

```yaml
name: Build and deploy
on:
  push:
    branches:
      - main
  workflow_dispatch:   # 手動トリガーも可能
```

#### ビルドジョブの主な処理

1. **リポジトリのチェックアウト**（サブモジュール含む）
2. **Go / Node.js / Dart Sass / Hugo のセットアップ**
3. **Hugo でサイトをビルド**（`hugo --gc --minify`）
4. **ビルド結果を GitHub Pages にアップロード**

#### 使用しているバージョン

| ツール | バージョン |
| --- | --- |
| Hugo | 0.152.2（extended） |
| Go | 1.25.3 |
| Node.js | 22.20.0 |
| Dart Sass | 1.93.2 |

Hugo の **extended 版** を使用しているため、Sass/SCSS の処理もサポートされています。

### デプロイジョブ

ビルドが完了すると、`actions/deploy-pages` アクションが `public/` ディレクトリの内容を GitHub Pages にデプロイします。

---

## ローカルでの開発

### 前提条件

- [Hugo（extended 版）](https://gohugo.io/installation/) がインストール済み
- [Git](https://git-scm.com/) がインストール済み

### リポジトリのクローン

```bash
git clone --recursive https://github.com/Key-2014/Key-2014.github.io.git
cd Key-2014.github.io
```

> `--recursive` を付けないと PaperMod テーマが取得されません。忘れた場合は：
> ```bash
> git submodule init
> git submodule update
> ```

### ローカルサーバーの起動

```bash
hugo server -D
```

- `-D` オプションで `draft: true` の下書き記事もプレビューできます
- ブラウザで `http://localhost:1313/` にアクセス
- ファイルを保存すると**自動でリロード**されます（ライブリロード）

### ビルドのみ実行

```bash
hugo --gc --minify
```

`public/` ディレクトリに静的サイトが出力されます。

---

## まとめ

| 項目 | 内容 |
| --- | --- |
| フレームワーク | Hugo（静的サイトジェネレータ） |
| テーマ | PaperMod（サブモジュール管理） |
| 記事の書き方 | `content/post/` に Markdown を置くだけ |
| デプロイ | GitHub Actions が自動でビルド & デプロイ |
| URL | https://Key-2014.github.io/ |

- **Markdown で記事を書く** → **push する** → **自動でサイトに反映** というシンプルなワークフロー
- テーマや CI/CD の設定はすでに整っているので、記事を書くことに集中できます

次のページ「[ページの編集・カスタマイズガイド](/post/site-editing-guide/)」では、実際に記事を書いたりサイトをカスタマイズする方法を解説します ✏️
