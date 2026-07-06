[English](README.md) | [한국어](README.ko.md) | [中文](README.zh.md) | 日本語 | [Español](README.es.md)

# show-me-the-prd

<p align="center">
  <img src="assets/show-me-the-prd-hero-01.png" alt="show-me-the-prd" width="320">
</p>

> **バイブコーダーのためのインタビュー型 PRD ジェネレーター — 一文のアイデアから 4 つの設計ドキュメントまで。**

ひとつのアイデアを完全なプロダクト計画に。5〜6 個の構造化された質問に答えるだけで、どの AI コーディングツールにもそのまま渡せる 4 つの設計ドキュメントが手に入ります。

[クイックスタート](#クイックスタート) • [なぜ show-me-the-prd なのか](#なぜ-show-me-the-prd-なのか) • [仕組み](#仕組み) • [主な機能](#主な機能) • [コマンド](#コマンド) • [動作要件](#動作要件)

---

## クイックスタート

### 1. マーケットプレイスを追加

```
/plugin marketplace add https://github.com/fivetaku/gptaku_plugins.git
```

### 2. プラグインをインストール

```
/plugin install show-me-the-prd
```

### 3. Claude Code を再起動

プラグインはセッション開始時に読み込まれます。インストール後に一度再起動してください。

### 4. 企画を始める

```
/show-me-the-prd I want to build a photo organization app
```

自然な言葉で話しかけるだけでも動きます：

```
Make me a PRD for a task manager
```

---

## なぜ show-me-the-prd なのか

- **企画の経験は不要** — すべての質問に、専門用語ではなく平易な言葉での説明とトレードオフが付いています
- **AI がリードし、あなたが決める** — AI がリアルタイムで選択肢を調査して提示します。自分で検索する必要はありません
- **当て推量ではなくリサーチ準拠** — 機能リスト、スタック比較、複雑度の評価はすべてライブの Web 検索に裏付けられています
- **1 つではなく 4 つのドキュメント** — PRD、データモデル、フェーズ計画、AI プロジェクト仕様が一貫したセットとして同時に生成されます
- **本番指向** — 各フェーズはローカルのモックではなく、実際のデプロイ・実際の認証・実際のデータベースを目標にします
- **既存の企画も歓迎** — 手元の仕様書を渡せば、プラグインがギャップを特定して埋めてくれます

---

## 仕組み

```
One-sentence idea
       |
       v
  [ Interview ]  ←── live web research runs between each question
       |
   Turn 1: clarify the idea (1–3 questions)
   Turn 2: pick core features + MVP scope
   Turn 3: confirm data model
   Turn 4: confirm phase breakdown
   Turn 5: choose tech stack + auth method
       |
       v
  [ 4 Documents ]
       |
       +── PRD/01_PRD.md            What you're building and who it's for
       +── PRD/02_DATA_MODEL.md     Core data structure (conceptual ERD)
       +── PRD/03_PHASES.md         Phase plan with start prompts
       +── PRD/04_PROJECT_SPEC.md   AI rules + "never do this" list
       +── PRD/README.md            Navigation guide
```

インタビューのすべての選択肢には、平易な言葉での説明、メリット、デメリット、複雑度の評価（Simple / Moderate / Complex）が付いています。迷ったら **(recommended)** と表示されたものを選べば大丈夫です。

---

## 主な機能

### インタビュー型の企画

開発者の語彙は要りません。「認証戦略を選んでください」の代わりに「ユーザーはどうやってログインしますか？」と尋ね、「ソーシャルログイン（Google/Kakao）」「メールアドレス + パスワード」のような具体的な選択肢を提示します。

### リアルタイム Web リサーチ

インタビューの各ターンの間に、関連機能・ありがちな落とし穴・現在のベストプラクティスなスタックをライブ検索します。選択肢に表示される内容は、ハードコードされた提案ではなく実際の検索結果に基づいています。

### 複雑度ガイド

機能選択ステップでは、各機能にラベルが付きます：

| ラベル | 意味 |
|-------|------|
| Simple | ほとんどのフレームワークでそのまま動く |
| Moderate | 外部サービスが必要。コストが発生する可能性あり |
| Complex | 最初のバージョンではスキップ推奨 |

### 4 つの成果物ドキュメント

| ドキュメント | 内容 | 使いどころ |
|----------|------|-------------|
| `01_PRD.md` | プロダクトの目標、ユーザーストーリー、機能リスト | プロジェクト開始時に共有 |
| `02_DATA_MODEL.md` | 中核エンティティとその関係 | データベース設計時に共有 |
| `03_PHASES.md` | 開始プロンプト付きのフェーズ別計画 | 実装順の参照に |
| `04_PROJECT_SPEC.md` | AI の行動ルール + 「絶対にやるな」リスト | 毎セッション AI に共有 |

### 企画補強モード

すでに仕様書やメモがある場合は、コマンドを実行する前に共有してください。プラグインがそれを読み、4 ドキュメント基準に照らして足りない部分を特定し、ギャップを埋めるのに必要な質問だけを行います。

---

## コマンド

| コマンド | 説明 |
|---------|-------------|
| `/show-me-the-prd [idea]` | アイデアを添えてインタビューを開始 |
| `/show-me-the-prd` | 最初の質問からインタラクティブに開始 |

### 自然言語トリガー

次のように話しかけても起動します：

- 「PRD を作って」
- 「企画書を作って」
- 「Show me the PRD」
- 「このアプリを企画して」
- 「[アイデア] を企画して」

---

## 動作要件

- [Claude Code](https://docs.anthropic.com/claude-code) CLI

### 推奨プラグイン（任意）

show-me-the-prd と一緒にインストールすると、リサーチの品質が上がります：

| プラグイン | 追加されるもの |
|--------|-------------|
| `docs-guide` | 技術スタック調査時の公式ドキュメント参照 |
| `insane-research` | より深い市場・トレンド分析 |

なくても問題なく動きます — 自動的に WebSearch にフォールバックします。

---

## ライセンス

MIT

---

<div align="center">

**一文を入れる。4 つのドキュメントが出てくる。**

</div>
