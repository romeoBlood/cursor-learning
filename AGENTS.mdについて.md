---
type: learning-note
domain: cursor
topic: AGENTS.md の役割と活用
source_type: doc
source:
  title: AGENTS.md 公式サイト / Cursor ドキュメント / Zenn 記事
  author: Agentic AI Foundation (Linux Foundation傘下)
  url: https://agents.md/
  date: 2026-03-04
created: 2026-03-04
updated: 2026-03-04
tags:
  - cursor
  - agents-md
  - claude-code
  - coding-agent
  - skills
---

# AGENTS.md の役割と活用

## 🧭 概要（30秒で思い出す）

- **何か**：AIコーディングエージェント向けの統一指示ファイル（Markdown形式）
- **なぜ重要か**：Cursor / Codex / Copilot / Gemini CLI など 20以上のツールが対応する**業界標準**。1つのファイルで複数エージェントに指示を出せる
- **Claude Code の CLAUDE.md に最も近い存在**：プロジェクトルートに置き、ビルド手順・コード規約・テスト方法などを記述する点が同じ。Cursor の `.cursor/rules/` よりもシンプルで、CLAUDE.md と同じ「plain Markdown」思想
- ==Cursor の 5 つの指示方法（AGENTS.md / Rules / Skills / Commands / Subagents）の中で、最もシンプルかつポータブルな「基本方針」担当==

---

## 📚 情報ソース

- 種別：公式Doc / Web記事
- タイトル：
  - AGENTS.md 公式
  - Cursor Rules ドキュメント
  - [Cursor の Skills, Rules, Commands 解説（Zenn）](https://zenn.dev/redamoon/articles/article38-cursor-skills-rules-commands)
- URL：
  - https://agents.md/
  - https://cursor.com/docs/context/rules
  - https://cursor.com/docs/context/skills
  - https://dev.to/proflead/what-is-agentsmd-and-why-should-you-care-3bg4
- メモ：Google・OpenAI・Factory・Sourcegraph・Cursor が共同で策定。現在は Linux Foundation 傘下の Agentic AI Foundation が管理。60,000以上の OSS プロジェクトで採用済み

---

## 🧩 要点まとめ

### AGENTS.md とは

- "README for agents" ― 人間向け README を補完する、**エージェント向けの文脈ファイル**
- 標準 Markdown。フロントマターやメタデータは不要
- プロジェクトルートに `AGENTS.md` を置くだけで対応エージェントが自動検出

### Cursor の 5 つの指示方法における位置づけ

==Cursor 2.4 以降、プロジェクト指示は 5 つの仕組みに整理された。AGENTS.md はその最もシンプルな基盤==：

| 仕組み | 性質 | 役割 |
|--------|------|------|
| **AGENTS.md** | ==常時適用・設定不要・オープン標準== | プロジェクトの基本方針 |
| **Rules** (.cursor/rules/) | 条件付き適用（glob/always/description） | 細かい制約・ファイル種別ごとのルール |
| **Skills** (.cursor/skills/) | 段階的開示・AI が自動判断してロード | 専門知識・ワークフロー注入 |
| **Commands** (.cursor/commands/) | `/コマンド名` で手動起動 | 定型作業のショートカット |
| **Subagents** (.cursor/agents/) | 独立コンテキスト・並列実行 | 長時間リサーチ・並列処理 |

> ==使い分けの原則：「すべてのエージェントに常に伝えたい基本方針 → AGENTS.md」「条件付きの細かいルール → Rules」「タスク固有の専門知識 → Skills」==

### CLAUDE.md との比較

| 観点 | AGENTS.md | CLAUDE.md |
|------|-----------|-----------|
| 対応ツール | Cursor, Codex, Copilot, Gemini CLI 他20+ | Claude Code 専用 |
| 形式 | plain Markdown | plain Markdown |
| 配置場所 | プロジェクトルート（ネスト可） | プロジェクトルート（ネスト可） |
| 読み込み | 最も近いファイルが優先 | 同様 |
| 標準化 | 業界横断のオープン標準 | Anthropic 独自 |
| 実質的な役割 | **ほぼ同じ** | **ほぼ同じ** |

→ 両者は設計思想がほぼ同一。`ln -s AGENTS.md CLAUDE.md` のようにシンボリックリンクで共用するチームもある

### Cursor の `.cursor/rules/` との比較

| 観点 | AGENTS.md | .cursor/rules/ (.mdc) |
|------|-----------|----------------------|
| 形式 | plain Markdown | フロントマター付き（glob パターン等） |
| スコープ制御 | ディレクトリ階層で暗黙的に | `alwaysApply` / `globs` で明示的に |
| 複雑さ | シンプル | きめ細かい制御が可能 |
| ポータビリティ | 他ツールでもそのまま使える | Cursor 専用 |

→ **AGENTS.md はシンプル・ポータブル**、**Rules はきめ細かい制御向き**。併用可能

### Cursor の Skills との比較

| 観点 | AGENTS.md | Skills (.cursor/skills/) |
|------|-----------|-------------------------|
| ロードタイミング | ==常時（毎リクエスト全文ロード）== | ==段階的開示（必要時のみ AI が判断してロード）== |
| トークン効率 | 書きすぎると非効率 | 段階的開示で効率的 |
| 内容の性質 | プロジェクト全体の基本方針 | タスク固有の専門知識・手順 |
| ポータビリティ | 他ツールでも使える | ==Agent Skills 標準で Cursor / Claude Code 等で相互運用可能== |

→ ==AGENTS.md に書くべきは「常に必要な基本方針」のみ。タスク固有の専門知識は Skills に分離してトークンを節約==

### 推奨セクション構成

```markdown
# Project Name

## Overview
プロジェクトの概要（1-2文）

## Setup
依存関係のインストール・ビルド手順

## Code Style
フォーマット・命名規則・設計パターン

## Testing
テスト実行コマンド・テスト方針

## Commit Guidelines
コミットメッセージ形式・ブランチ戦略

## Security
注意すべきセキュリティ事項

## Architecture
ディレクトリ構成・主要コンポーネント
```

> **ポイント**
> AGENTS.md は毎リクエストで全トークンが読み込まれるため、**簡潔に保つことが最重要**。CLAUDE.md と同じ原則。タスク固有の詳細は Skills に移すのがベストプラクティス。

---

## 🧪 具体例・検証

### 基本的な AGENTS.md の例

```markdown
# My Project

## Overview
TypeScript + React のフロントエンドアプリケーション

## Setup
npm install && npm run dev

## Code Style
- TypeScript strict mode
- 関数コンポーネント + hooks
- named export を使用

## Testing
npm test           # 全テスト
npm test -- --watch  # ウォッチモード

## Commit Guidelines
Conventional Commits 形式: feat: / fix: / docs: / refactor:
```

### モノレポでのネスト例

```
project/
  AGENTS.md              ← グローバル指示
  frontend/
    AGENTS.md            ← フロントエンド固有の指示
  backend/
    AGENTS.md            ← バックエンド固有の指示
```

- エージェントは作業対象ディレクトリに最も近い AGENTS.md を優先的に読む
- 親ディレクトリの指示とマージされる

### AGENTS.md + Skills + Rules の使い分け例

```text
project/
  AGENTS.md                         ← 基本方針（常時ロード）
    → プロジェクト概要、ビルド手順、命名規則
  .cursor/
    rules/typescript.mdc            ← ファイル条件付きルール
      → *.ts ファイルに対する strict mode 規約
    skills/deploy-app/SKILL.md      ← 専門知識（必要時のみロード）
      → デプロイ手順・環境変数・注意事項
    skills/review/SKILL.md          ← 専門知識（必要時のみロード）
      → コードレビューの観点・チェックリスト
```

### CLAUDE.md との共用

```bash
# AGENTS.md をマスターにして CLAUDE.md はシンボリックリンク
ln -s AGENTS.md CLAUDE.md
```

---

## ⚠️ つまずきポイント・アンチパターン

- **書きすぎ**：毎リクエストで全文がロードされるため、トークン消費が増えて応答品質が下がる。本当に必要な指示だけに絞る
- ==**タスク固有の知識まで AGENTS.md に書く**==：デプロイ手順やレビュー観点など、常に必要でない情報は Skills に分離すべき（段階的開示で効率的）
- **Rules と内容が重複**：Cursor の `.cursor/rules/` と AGENTS.md で同じことを書くとメンテが二重になる。AGENTS.md は基本方針、Rules は条件付きの細かいルールと役割を分ける
- **更新忘れ**：プロジェクト構成が変わったのに AGENTS.md が古いまま → エージェントが間違った手順を実行する
- **CLAUDE.md との不整合**：両方あるのに内容が違うと、使うツールによって挙動が変わる。シンボリックリンクか単一ファイルに統一が安全

---

## 🔁 Claude Code での活用方法

- Claude Code は AGENTS.md を**直接サポートしていない**（CLAUDE.md を使用）
- ただし AGENTS.md と CLAUDE.md は形式・目的が同一なので、以下の方法で統合できる：

1. **シンボリックリンク方式**（推奨）
```bash
# プロジェクトルートで
echo "共通の指示内容" > AGENTS.md
ln -s AGENTS.md CLAUDE.md
```

2. **CLAUDE.md から AGENTS.md を参照**
```markdown
<!-- CLAUDE.md -->
See AGENTS.md for project-wide instructions.
（Claude Code は AGENTS.md を自動では読まないが、会話中に参照を指示できる）
```

例：

```text
このプロジェクトの AGENTS.md を読んで、その規約に従ってコードを書いてください
```

---

## 🔗 関連ノート

- [[CursorとClaude_Codeの違い]] - Cursor と Claude Code の詳細比較（Skills / Subagents 含む）
- [[Cursorにおけるモデル選択について]] - モデル選択の考え方

---

## 📝 自分の言葉で一言まとめ

> 「AGENTS.md は Cursor の 5 つの指示方法の中で最もシンプルな "基本方針" 担当。CLAUDE.md のオープン標準版であり、常に必要な情報だけを簡潔に書く。タスク固有の知識は Skills に、条件付きルールは Rules に分離するのが正しい使い分け。」

---

## 📌 TODO / 次に調べること

- [ ] 実際に AGENTS.md を作成して Cursor Agent の挙動変化を検証
- [ ] CLAUDE.md とのシンボリックリンク運用を試す
- [ ] AGENTS.md + Rules + Skills の三位一体運用でトークン効率を比較検証
- [ ] Vercel の「AGENTS.md が skills より評価で上回った」記事を読む → https://vercel.com/blog/agents-md-outperforms-skills-in-our-agent-evals
