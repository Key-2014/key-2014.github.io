---
date: '2026-04-07'
draft: false
title: 'ページの編集・カスタマイズガイド'
description: '新しい記事の作成方法、Markdown の書き方、画像の追加、メニューのカスタマイズ、テーマの変更まで、サイトを自分で編集するための完全ガイドです。'
tags: ['Hugo', 'PaperMod', 'Markdown', 'カスタマイズ', 'ガイド']
categories: ['Tech']
ShowToc: true
TocOpen: true
---

## はじめに

このページでは、**Key-2014.github.io** に新しい記事を追加したり、既存のページを編集したりする方法を解説します。

前提知識として「[Key-2014.github.io の作り方と仕組み](/post/site-architecture-guide/)」を読んでおくとスムーズです。

---

## 新しい記事を作成する

### 方法 1: Hugo コマンドを使う（推奨）

```bash
hugo new content/post/my-new-article.md
```

`archetypes/default.md` をテンプレートとして、以下のような Front Matter 付きファイルが自動生成されます：

```markdown
---
date: '2026-04-07T14:00:00+09:00'
draft: true
title: 'My New Article'
---
```

### 方法 2: 手動でファイルを作成する

`content/post/` ディレクトリに `.md` ファイルを直接作成しても OK です。その場合、以下の Front Matter を手書きします。

---

## Front Matter（記事のメタ情報）

すべての記事ファイルの先頭には **Front Matter** と呼ばれるメタ情報ブロックを記述します。これは `---` で囲まれた YAML 形式のデータです。

### 基本的な Front Matter

```yaml
---
date: '2026-04-07'
draft: false
title: '記事のタイトル'
description: '検索結果やSNSシェア時に表示される説明文'
tags: ['Hugo', 'ガイド']
categories: ['Tech']
ShowToc: true
TocOpen: true
---
```

### 各項目の説明

| 項目 | 必須 | 型 | 説明 |
| --- | --- | --- | --- |
| `date` | ✅ | 文字列 | 記事の日付（`YYYY-MM-DD` 形式） |
| `draft` | ✅ | 真偽値 | `true` だと本番に表示されない（下書き） |
| `title` | ✅ | 文字列 | 記事のタイトル |
| `description` | - | 文字列 | 記事の概要。SEO やカード表示に使われる |
| `tags` | - | リスト | タグ（記事のキーワード） |
| `categories` | - | リスト | カテゴリ |
| `ShowToc` | - | 真偽値 | 目次を表示するか（デフォルト: サイト設定に従う） |
| `TocOpen` | - | 真偽値 | 目次を開いた状態で表示するか |
| `weight` | - | 数値 | 記事の並び順（小さいほど先頭） |
| `cover.image` | - | 文字列 | カバー画像のパス |

### 下書きについて

`draft: true` の記事は：
- ✅ `hugo server -D` でローカルプレビュー可能
- ❌ 本番ビルド（GitHub Actions）で除外される

記事が完成したら `draft: false` に変更してから push してください。

---

## Markdown の書き方

Hugo は標準的な Markdown に加えて、いくつかの拡張記法をサポートしています。

### 見出し

```markdown
## 大見出し（h2）
### 中見出し（h3）
#### 小見出し（h4）
```

> ⚠️ `# h1` 見出しは記事内では使わないでください。`title` が h1 として表示されます。

### テキストの装飾

```markdown
**太字**
*斜体*
~~取り消し線~~
`インラインコード`
```

### リスト

```markdown
- 箇条書き 1
- 箇条書き 2
  - ネスト

1. 番号付きリスト
2. 二番目
```

### リンク

```markdown
[表示テキスト](https://example.com)
[サイト内リンク](/post/site-architecture-guide/)
```

### 画像

```markdown
![代替テキスト](/images/screenshot.png)
```

画像ファイルは `static/images/` ディレクトリに配置すると、`/images/` パスでアクセスできます。

### コードブロック

````markdown
```python
def hello():
    print("Hello, World!")
```
````

言語名を指定すると**シンタックスハイライト**が適用されます。PaperMod テーマではコピーボタンも表示されます。

### テーブル

```markdown
| 列 1 | 列 2 | 列 3 |
| --- | --- | --- |
| データ 1 | データ 2 | データ 3 |
```

### 引用

```markdown
> これは引用文です。
> 複数行も可能です。
```

### 区切り線

```markdown
---
```

### 絵文字

Hugo では `enableEmoji: true` が設定されているので、絵文字コードが使えます：

```markdown
:rocket: → 🚀
:sparkles: → ✨
:book: → 📖
```

---

## 画像の追加方法

### 1. `static` ディレクトリに配置

```
static/
└── images/
    └── my-screenshot.png
```

Markdown からの参照：

```markdown
![スクリーンショット](/images/my-screenshot.png)
```

### 2. 記事と同じディレクトリに配置（Page Bundle）

ページバンドルを使うと、記事と画像をまとめて管理できます：

```
content/post/my-article/
├── index.md       # 記事本体
└── screenshot.png  # 画像ファイル
```

Markdown からは相対パスで参照：

```markdown
![スクリーンショット](screenshot.png)
```

### カバー画像を設定する

Front Matter で `cover` を指定すると、記事のカバー画像（アイキャッチ）を表示できます：

```yaml
---
title: '記事タイトル'
cover:
  image: '/images/cover.png'
  alt: 'カバー画像の説明'
  caption: 'キャプション'
  relative: false
---
```

---

## メニューの編集

ナビゲーションメニュー（ヘッダー部分）は `hugo.yaml` の `menu.main` で管理します。

### メニュー項目を追加する

```yaml
menu:
  main:
    - name: "About Me"
      url: /aboutme
      weight: 1
    - name: "Posts"
      url: /post/
      weight: 3
    - name: "Archives"       # ← 追加例
      url: /archives/
      weight: 5
```

- `name`: メニューに表示する名前
- `url`: リンク先のパス
- `weight`: 表示順（小さいほど左＝先頭）

### Archives ページの作成例

`content/archives.md` を作成：

```markdown
---
title: "Archives"
layout: "archives"
url: "/archives/"
summary: "archives"
---
```

---

## サイト設定のカスタマイズ

`hugo.yaml` で変更可能な主な設定を紹介します。

### サイトタイトルの変更

```yaml
title: Key's Sandbox    # ← お好みのタイトルに
```

### 説明文の変更

```yaml
params:
  description: "Writing about tech articles and daily events."
```

### ソーシャルリンクの編集

```yaml
params:
  socialIcons:
    - name: github
      url: "https://github.com/Key-2014"
    - name: email
      url: "keionuma1214@gmail.com"
    - name: twitter           # ← 追加例
      url: "https://twitter.com/your_handle"
```

利用可能なアイコン名は [PaperMod のドキュメント](https://adityatelange.github.io/hugo-PaperMod/posts/papermod/papermod-icons/) で確認できます。

### テーマカラーの切替

```yaml
params:
  defaultTheme: "auto"    # "auto" / "light" / "dark"
```

| 値 | 動作 |
| --- | --- |
| `auto` | OS のダーク/ライト設定に追従 |
| `light` | 常にライトモード |
| `dark` | 常にダークモード |

### ホームページの設定

```yaml
params:
  homeInfoParams:
    Title: "*Hello!"
    Content: |
      Welcome to my blog!
```

---

## テーマのカスタマイズ

PaperMod テーマのレイアウトやスタイルを変更したい場合、**テーマのファイルを直接編集しない**でください。代わりに、プロジェクトルートの `layouts/` ディレクトリにファイルを配置してオーバーライドします。

### レイアウトのオーバーライド

Hugo はテーマよりもプロジェクトルートの `layouts/` を優先します。たとえば、記事のフッターをカスタマイズしたい場合：

1. テーマ内の対象ファイルを見つける（例: `themes/PaperMod/layouts/partials/footer.html`）
2. 同じパスでプロジェクトにコピー（`layouts/partials/footer.html`）
3. コピーしたファイルを編集

```bash
# 例: フッターレイアウトをオーバーライド
mkdir -p layouts/partials
cp themes/PaperMod/layouts/partials/footer.html layouts/partials/footer.html
```

### カスタム CSS の追加

`assets/css/extended/` ディレクトリに CSS ファイルを配置すると、テーマの CSS に追加で読み込まれます：

```
assets/
└── css/
    └── extended/
        └── custom.css
```

例: リンクの色を変更する：

```css
/* assets/css/extended/custom.css */
:root {
  --link-color: #3498db;
}

a {
  color: var(--link-color);
}
```

---

## 記事を公開する手順

記事を書いてからサイトに反映するまでの手順をまとめます。

### 1. 記事を作成

```bash
hugo new content/post/my-new-article.md
```

### 2. 記事を編集

お好みのエディタ（VS Code 等）で Markdown を編集します。

### 3. ローカルでプレビュー

```bash
hugo server -D
```

ブラウザで `http://localhost:1313/` にアクセスして確認。

### 4. 下書きフラグを解除

```yaml
draft: false    # true → false に変更
```

### 5. コミット & プッシュ

```bash
git add content/post/my-new-article.md
git commit -m "Add: 新しい記事を追加"
git push origin main
```

### 6. 自動デプロイを確認

GitHub の **Actions** タブでワークフローが成功していることを確認します。数分後にサイトに反映されます。

---

## よくある操作

### 記事を削除する

対象の `.md` ファイルを削除して push するだけです：

```bash
rm content/post/old-article.md
git add -A
git commit -m "Remove: 古い記事を削除"
git push origin main
```

### 記事を非公開にする

ファイルを削除せず、Front Matter で `draft: true` に変更する方法もあります：

```yaml
draft: true   # サイトに表示されなくなる
```

### テーマを更新する

```bash
git submodule update --remote themes/PaperMod
git add themes/PaperMod
git commit -m "Update: PaperMod テーマを更新"
git push origin main
```

---

## トラブルシューティング

### ローカルで `hugo server` が起動しない

```bash
# Hugo がインストールされているか確認
hugo version

# サブモジュールが初期化されているか確認
git submodule status
# 「-」から始まる行がある場合、サブモジュールが未初期化
git submodule init
git submodule update
```

### 記事が表示されない

以下を確認してください：

1. **`draft: false` になっているか** — `draft: true` だと本番に表示されません
2. **日付が未来になっていないか** — Hugo はデフォルトで未来の日付の記事を表示しません
3. **ファイルが `content/post/` にあるか** — 別のディレクトリに置くと一覧に表示されない場合があります

### 画像が表示されない

- `static/` ディレクトリに配置しているか確認
- パスが `/images/file.png`（先頭に `/`）になっているか確認
- ファイル名の大文字小文字が一致しているか確認（Linux はケースセンシティブ）

### GitHub Actions のビルドが失敗する

1. GitHub の **Actions** タブでエラーログを確認
2. よくある原因：
   - Front Matter の YAML 構文エラー（インデントのずれ等）
   - 壊れたリンクや参照
   - サブモジュールの取得失敗

---

## まとめ

| やりたいこと | 方法 |
| --- | --- |
| 記事を書く | `content/post/` に `.md` ファイルを作成 |
| プレビュー | `hugo server -D` でローカル確認 |
| 公開する | `draft: false` にして push |
| メニュー編集 | `hugo.yaml` の `menu.main` を編集 |
| スタイル変更 | `assets/css/extended/` にカスタム CSS を配置 |
| レイアウト変更 | `layouts/` にテーマファイルをコピーして編集 |
| テーマ更新 | `git submodule update --remote` |

慣れてしまえば、**Markdown を書いて push するだけ** でサイトが更新できます。ぜひいろいろ試してみてください 🎉
