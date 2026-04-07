---
date: '2026-04-07'
draft: false
title: 'Typst テンプレートの紹介と使い方'
description: '個人用 Typst テンプレート (Typst_Template) を Git サブモジュールとして導入し、レポートや課題を統一レイアウトで効率的に作成する方法を解説します。'
tags: ['Typst', 'テンプレート', 'Git', 'サブモジュール', 'LaTeX代替']
categories: ['Tech']
ShowToc: true
TocOpen: true
---

## はじめに

大学の授業で提出するレポートや課題を Typst で作成するとき、毎回同じフォーマット設定をコピペしていませんか？

年度や授業ごとにリポジトリを分けていると、テンプレートの修正が必要になったとき全リポジトリを手動で更新する羽目に…。

そこで、**テンプレートを 1 つのリポジトリに集約し、Git サブモジュールとして使い回す** 仕組みを構築しました。

👉 リポジトリ: [**Typst_Template**](https://github.com/Key-2014/Typst_Template)

---

## なぜサブモジュール？

| 方式 | メリット | デメリット |
| --- | --- | --- |
| コピペ | 手軽 | 修正時に全リポジトリを手動更新 |
| Typst パッケージ | 公式の仕組み | 公開パッケージ登録が必要 |
| **Git サブモジュール** | **一元管理・即反映** | Git の知識がやや必要 |

サブモジュールなら、テンプレートを更新したあと各プロジェクトで `git submodule update --remote` を実行するだけで最新版に同期できます。

---

## テンプレートの特徴

### 🇯🇵 日本語最適化

- フォント: **原ノ味明朝（Harano Aji Mincho）** + **New Computer Modern Math**
- 段落スタイル: 両端揃え・字下げ対応
- 言語設定: `lang: "ja"` で日本語ドキュメントとして最適化

### 📐 数式サポート

- `physica` パッケージによる物理学記法
- `unify` パッケージで単位の統一表記（`qty`, `num`）
- カスタム数式番号: 標準 `(1)` とセクション連動 `(1.2.3)` を切替可能

### 📊 図表・作図

- `cetz` パッケージによるベクター作図
- `showybox` で装飾ボックス
- `whalogen` で化学式表記
- 図表キャプション: 自動で「図」「表」と日本語表記

---

## インストール方法

### 1. サブモジュールとして追加

使用したいプロジェクトのルートディレクトリで以下を実行します：

```bash
# lib ディレクトリにサブモジュールとして追加
mkdir -p lib
git submodule add https://github.com/Key-2014/Typst_Template.git lib/my-templates
```

### 2. 既存リポジトリをクローンする場合

サブモジュールを含むリポジトリをクローンする際は、`--recursive` オプションを付けます：

```bash
git clone --recursive https://github.com/<ユーザー名>/<リポジトリ名>.git
```

もしクローン後にサブモジュールの初期化を忘れた場合は：

```bash
git submodule init
git submodule update
```

### 3. テンプレートを最新版に更新

```bash
git submodule update --remote lib/my-templates
git add lib/my-templates
git commit -m "Update Typst_Template to latest"
```

---

## 使い方

### 基本的な使い方

メインの `.typ` ファイルでテンプレートをインポートし、`project` 関数を適用します：

```typ
// テンプレートライブラリのインポート
#import "lib/my-templates/lib.typ": *

// ドキュメント全体にテンプレートを適用
#show: project.with(
  title: "レポートのタイトル",
  author: "氏名",
  student-id: "学生番号",
  date: "2026年4月7日",
)

= はじめに
ここから本文を書き始めます。
日本語フォントや数式環境がすでに設定済みです。
```

### `project` 関数の設定オプション

| 引数 | 型 | デフォルト | 説明 |
| --- | --- | --- | --- |
| `title` | `content` | `""` | ドキュメントのタイトル |
| `author` | `content` | `""` | 著者名 |
| `student-id` | `content` | `""` | 学生番号 |
| `date` | `content` | `none` | 日付 |
| `textbook-numbering` | `boolean` | `false` | `true` で教科書スタイルの数式番号 |
| `indent` | `boolean` | `true` | `false` で字下げを無効化 |
| `heading-numbering` | `string` | `none` | 見出しの番号付け形式（例: `"1.1.1"`） |
| `heading-supplement` | `content` | `none` | 見出しの補足テキスト |

---

## ユーティリティマクロ

`utils.typ` には、レポート執筆を効率化する便利なマクロが含まれています。

### 数学・物理系

| マクロ | 説明 | 出力例 |
| --- | --- | --- |
| `#combination(n, r)` | 組合せ記号 | ₙCᵣ |
| `#permutation(n, r)` | 順列記号 | ₙPᵣ |
| `#qed` | QED 記号（右寄せ） | ∎ |
| `#lhs` / `#rhs` | 「(左辺)」/「(右辺)」 | (左辺) / (右辺) |

### ボックス系

| マクロ | 説明 | 用途 |
| --- | --- | --- |
| `#crect(body)` | 中央揃え枠 | 定義や定理の強調 |
| `#frect(body)` | 全幅枠 | 重要な式のハイライト |
| `#ans(body)` | 解答ボックス | 最終回答の囲み |

### 単位系

```typ
// 単位を正体（upright）で表示
$10 #u("kg")$         // → 10 kg
$5 #u("m/s^2")$       // → 5 m/s²
$3 #u("m", b: true)$  // → 3 [m]  （括弧付き）
```

### 数式タグ

```typ
// グローバルな数式カウンタに影響を与えずにタグを付ける
#eqtag($E = m c^2$, "Einstein")
// → 式の番号が (Einstein) と表示される
```

---

## 数式番号のスタイル

### 標準スタイル（デフォルト）

```typ
#show: project.with(title: "標準スタイル")
// 数式番号: (1), (2), (3), ...
```

### セクション連動スタイル

見出し番号を設定すると、数式番号がセクションに連動します：

```typ
#show: project.with(
  title: "教科書スタイル",
  heading-numbering: "1.1",
)
// 数式番号: (1.1), (1.2), (2.1), ...
```

---

## プロジェクト構成例

実際にサブモジュールとして利用する場合のディレクトリ構成例です：

```
my-report/
├── lib/
│   └── my-templates/        # ← Git サブモジュール (Typst_Template)
│       ├── lib.typ           # メインテンプレート
│       └── utils.typ         # ユーティリティマクロ
├── main.typ                  # レポート本体
├── figures/                  # 図表ファイル
├── .gitmodules               # サブモジュール設定
└── README.md
```

---

## 含まれるパッケージ一覧

テンプレートに同梱されている Typst Preview パッケージ：

| パッケージ | バージョン | 用途 |
| --- | --- | --- |
| [physica](https://typst.app/universe/package/physica) | 0.9.7 | 物理学記法（偏微分、ブラケット等） |
| [unify](https://typst.app/universe/package/unify) | 0.7.1 | SI 単位の統一表記 |
| [cetz](https://typst.app/universe/package/cetz) | 0.4.2 | ベクター作図 |
| [showybox](https://typst.app/universe/package/showybox) | 2.0.4 | 装飾ボックス |
| [whalogen](https://typst.app/universe/package/whalogen) | 0.3.0 | 化学式表記 |

---

## まとめ

- **Typst_Template** は、日本語レポート作成に必要な設定をすべて詰め込んだテンプレート
- **Git サブモジュール** として導入すれば、複数プロジェクトで一元管理できる
- テンプレートの更新は `git submodule update --remote` 一発で反映
- `project` 関数の引数で、レイアウトや数式番号スタイルを柔軟にカスタマイズ可能

テンプレートのソースコードや最新情報は [GitHub リポジトリ](https://github.com/Key-2014/Typst_Template) をご覧ください 📄
