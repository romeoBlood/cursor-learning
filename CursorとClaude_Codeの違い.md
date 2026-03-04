---
type: learning-note
domain: claude-code
topic: Cursor と Claude Code の違い
source_type: experiment
source:
  title: Claude Code との会話による比較検証 + Cursor 公式ドキュメント
  author:
  url: https://zenn.dev/redamoon/articles/article38-cursor-skills-rules-commands
  date: 2026-03-04
created: 2026-03-04
updated: 2026-03-04
tags:
  - claude-code
  - cursor
  - learning
  - comparison
  - agent
  - subagent
  - skills
---

# Cursor と Claude Code の違い

## 🧭 概要（30秒で思い出す）

- Cursor は GUI（VS Code フォーク）× マルチモデル、Claude Code は CLI × Claude 専用
- ==Cursor 2.4 でサブエージェント・スキル・カスタムエージェントが追加され、エージェント機能の差は大幅に縮小==
- ==日常的なコーディング支援（Tab 補完・GUI 操作）では Cursor が便利。CLI ベースの自律作業・権限制御では Claude Code が優位。併用が最も効果的==

### 一目でわかるエージェント機能比較（2026年3月時点）

| 機能           | Claude Code                            | Cursor（2.4+）                                |
| ------------ | -------------------------------------- | ------------------------------------------- |
| サブエージェント並列起動 | Explore / Plan / General-purpose を同時起動 | ==Explore / Bash / Browser + カスタム（最大10並列）== |
| バックグラウンド実行   | 非同期で並列実行可能                             | ==対応（background フラグ）==                      |
| ワークツリー分離     | git worktree で安全に並列作業                  | 非対応                                         |
| スキル（拡張コマンド）  | カスタム定義可能（ツール制限・モデル指定・引数展開）             | ==対応（段階的開示 + SKILL.md）==                    |
| 自律的なタスク分解    | TodoList で自己管理・進捗追跡                    | 限定的                                         |
| 永続メモリ        | `.claude/` 配下にセッション間で自動蓄積              | ==非対応（セッションごとにリセット）==                       |
| 大規模並列変更      | `/batch` で worktree 並列 + 自動 PR         | 非対応                                         |
| 権限の細粒度制御     | ツール・パス・ドメイン単位で Allow/Deny              | エディタの信頼設定に依存                                |

---

## 📚 情報ソース

- 種別：実験 / 公式ドキュメント / Web 記事
- タイトル：
  - [Cursor Skills, Rules, Commands 解説（Zenn）](https://zenn.dev/redamoon/articles/article38-cursor-skills-rules-commands)
  - [Cursor Agent Skills ドキュメント](https://cursor.com/docs/context/skills)
  - [Cursor Subagents ドキュメント](https://cursor.com/docs/context/subagents)
  - [Agent Skills vs. Rules vs. Commands（builder.io）](https://www.builder.io/blog/agent-skills-rules-commands)
- メモ：Cursor 2.4（2026年2月）でサブエージェント・スキルが正式導入され、従来の比較が大きく変わった

---

## 🧩 要点まとめ

### 基本情報

| 項目 | Cursor | Claude Code |
|------|--------|-------------|
| 形態 | GUI（VS Code フォーク） | CLI（ターミナル + エディタ拡張） |
| 対応モデル | Claude, GPT-4o, Gemini 等 | Claude 専用（Opus / Sonnet / Haiku） |
| 主な操作場所 | エディタ内チャットパネル | ターミナル / VS Code・JetBrains 拡張 |
| 料金体系 | Pro $20/月, Business $40/月 等 | Max $100/月 または API 従量課金 |

### Cursor の 5 つの指示方法（最新）

Cursor は現在、プロジェクト指示を **5 つの仕組み**で管理する：

| 仕組み | 配置場所 | 性質 | 一言で |
|--------|---------|------|--------|
| **AGENTS.md** | プロジェクトルート | 常時適用・設定不要 | 基本方針を簡潔に |
| **Rules** | `.cursor/rules/*.mdc` | 条件付き適用（glob / always / description） | 細かい制約 |
| **Skills** | `.cursor/skills/<name>/SKILL.md` | ==段階的開示・AI が必要時に自動ロード== | 専門知識注入 |
| **Commands** | `.cursor/commands/*.md` | `/コマンド名` で手動起動 | 定型作業の自動化 |
| **Subagents** | `.cursor/agents/*.md` | 独立コンテキスト・並列実行 | 長時間・並列タスク |

> **使い分けの原則**: ==Rules は「常に守るべきルール」、Skills は「必要な時に呼ばれる専門知識」、Commands は「ユーザーが明示的に起動するショートカット」==

### ルール／プロジェクト指示

| 観点 | Cursor | Claude Code |
|------|--------|-------------|
| ファイル形式 | `.mdc`（YAML frontmatter） | `.md`（標準 Markdown） |
| 設定場所 | `.cursor/rules/*.mdc` | `CLAUDE.md` / `.claude/rules/*.md` |
| 適用条件 | glob パターン / description / always | パスベースの自動ロード + paths 指定 |
| インポート機能 | `@filename` でコード参照 | `@path` 構文で再帰インポート（最大5ホップ） |
| 管理者制御 | Team Dashboard | Managed Policy（OS レベル） |
| オープン標準 | AGENTS.md 対応 | CLAUDE.md 独自形式 |

Cursor の 4 つのルールタイプ: Always Apply / Auto Attached（globs） / Agent Requested（description） / Manual（@呼び出し）

### スキル比較（大幅更新）

| 観点 | Cursor Skills | Claude Code Skills |
|------|--------------|-------------------|
| 配置場所 | `.cursor/skills/` / `.agents/skills/` | `.claude/skills/<name>/SKILL.md` |
| 読み込み方式 | ==段階的開示（Level 1: メタデータ → Level 2: 全文 → Level 3: 追加ファイル）== | プロンプト展開（全文ロード） |
| 自動呼び出し | ==AI が関連性を判断して自動ロード== | ==AI が必要に応じて自動呼び出し可能== |
| 手動呼び出し | `disable-model-invocation` で制限可能 | `/スキル名` で手動起動 |
| 実行制御 | 基本的になし | ツール制限、モデル指定、サブエージェント指定 |
| 互換性 | ==`.claude/skills/` も検出可能（相互運用）== | Cursor Skills フォーマットも利用可 |
| 並列実行 | なし | `/batch` でワークツリー並列実行 |

→ ==両ツールのスキルは Agent Skills オープン標準に基づき、互換性がある==

### コマンド比較

| 観点 | Cursor Commands | Claude Code Skills（コマンド的用途） |
|------|----------------|--------------------------------------|
| 本質 | ==保存済みプロンプトのショートカット== | ワークフロー定義（ツール・モデル・権限を含む） |
| 設定場所 | `.cursor/commands/*.md` | `.claude/skills/<name>/SKILL.md` |
| 起動方法 | `/コマンド名` | `/スキル名` |
| 引数展開 | パラメータ対応 | `$ARGUMENTS` / `$0` で位置引数対応 |

### サブエージェント比較（大幅更新）

| 観点 | Claude Code | Cursor（2.4+） |
|------|-------------|----------------|
| デフォルト種別 | Explore / Plan / General-purpose | ==Explore / Bash / Browser== |
| カスタム定義 | `.claude/agents/*.md` | ==`.cursor/agents/*.md`== |
| 並列実行 | 対応（複数同時起動） | ==対応（最大10並列）== |
| バックグラウンド | 対応 | ==対応（`background: true`）== |
| ネスト（子→孫） | 対応 | ==対応（サブエージェントがサブエージェントを起動）== |
| モデル指定 | `model` パラメータ（sonnet / opus / haiku） | ==`model` パラメータ（fast / inherit / 特定ID）== |
| 読み取り専用 | 一部サブエージェントで制限 | ==`readonly: true` で制限可能== |
| ワークツリー分離 | ==`isolation: worktree` で git worktree 利用== | 非対応 |

→ ==Cursor 2.4 でサブエージェント機能が追加され、Claude Code との差は大幅に縮小。ただし worktree 分離と `/batch` は Claude Code 固有==

### モード

| Cursor のモード | Claude Code の対応 |
|----------------|-------------------|
| **Agent**（デフォルト） | 通常モード（同等） |
| **Ask**（読み取り専用） | 専用モードなし（サブエージェントで代替） |
| **Plan**（計画作成） | Plan モード（同等） |
| **Debug**（バグ調査） | 専用モードなし（サブエージェントで代替） |
| **Custom Modes** | 対応なし |

### コンテキスト管理

- **Cursor**: `@Files` `@Code` `@Codebase` `@Docs` 等の `@` シンボルで==明示的に指定==。Notepads で再利用可能なコンテキスト管理
- **Claude Code**: ファイル読み取り・サブエージェント（Explore）による==自動コンテキスト収集==が中心。CLAUDE.md で永続知識供給、==Auto Memory でセッション間保持==

### Hooks（自動化）

| 観点 | Cursor | Claude Code |
|------|--------|-------------|
| 設定ファイル | `.cursor/hooks.json` | `settings.json` |
| イベント | sessionStart, preToolUse, postToolUse 等 | PreToolUse, PostToolUse, SubagentStart 等 |
| ブロック | 終了コード `2` | 終了コード `2`（同様） |
| 追加機能 | — | HTTP / LLM ベースのフック、サブエージェントのライフサイクルフック |

### MCP サーバー

| 観点 | Cursor | Claude Code |
|------|--------|-------------|
| 設定ファイル | `.cursor/mcp.json` | `.mcp.json` / `~/.claude.json` |
| トランスポート | stdio, SSE, Streamable HTTP | stdio, SSE (deprecated), HTTP |
| ツール検索 | なし | ツール数が多い場合に自動有効化 |
| プロンプト公開 | なし | MCP プロンプトが `/mcp__server__prompt` コマンドになる |

### 権限モデル

| 観点 | Cursor | Claude Code |
|------|--------|-------------|
| 基本方針 | 暗黙的に許可 | 明示的な許可制（ツールごと） |
| モード | エディタの信頼設定に依存 | default / acceptEdits / plan / dontAsk / bypassPermissions |
| 細粒度制御 | 限定的 | ツール・パス・ドメイン単位で Allow/Deny/Ask |
| サンドボックス | なし | ==OS レベルのファイルシステム・ネットワーク制限== |

> **ポイント**
> ==Cursor 2.4 で機能差は大幅に縮小した。残る Claude Code 固有の強みは「worktree 分離」「/batch 並列PR」「永続メモリ」「権限の細粒度制御」「サンドボックス」。Cursor 固有の強みは「Tab 補完」「GUI」「マルチモデル」「Custom Modes」。==

---

## 🧪 具体例・検証

### Cursor 固有の強み: Tab 補完

```text
- 複数行編集予測（diff ポップアップ表示）
- ファイル内ナビゲーション予測（次の編集位置を予測してジャンプ提案）
- クロスファイルジャンプ（関連ファイルの編集をポータルウィンドウで提示）
- 自動インポート（TypeScript / Python で不足するインポートを自動追加）
- Tab で受け入れ、Escape で拒否、Cmd+→ で単語単位の部分受け入れ
```

### 両ツール共通: サブエージェント並列処理

```text
【Claude Code】
1つのタスクに対して:
  - Explore エージェント → コードベース調査（並列起動）
  - Plan エージェント   → 設計方針策定（並列起動）
  - 本体               → 結果を統合して実装
  - バックグラウンド     → テスト実行（非同期）

サブエージェント種別:
  - Explore: 高速読み取り専用（Haiku）でコードベース検索
  - Plan: 調査して計画を提示
  - General-purpose: フルツールアクセスの汎用エージェント
  - カスタム: .claude/agents/ にファイル配置で独自定義可能

【Cursor 2.4+】
デフォルトサブエージェント:
  - Explore: コードベース検索・分析（高速モデル）
  - Bash: シェルコマンド実行（冗長出力のフィルタリング）
  - Browser: MCP 経由のブラウザ制御（DOM / スクリーンショット）
  - カスタム: .cursor/agents/ にファイル配置で独自定義可能

並列実行: 最大10タスク同時実行
ネスト: サブエージェントがさらにサブエージェントを起動可能
```

### Cursor スキル定義の例

```yaml
# .cursor/skills/deploy-app/SKILL.md
---
name: deploy-app
description: "アプリケーションのデプロイ手順。デプロイ関連の質問時に使用"
---

# Deploy App

## When to Use
- 本番・ステージング環境へのデプロイ時
- デプロイスクリプトの修正時

## Steps
1. テストを実行: `npm test`
2. ビルド: `npm run build`
3. デプロイ: `scripts/deploy.sh <environment>`

## Notes
- 本番デプロイ前に必ずステージングで確認
```

### Cursor サブエージェント定義の例

```yaml
# .cursor/agents/researcher.md
---
name: researcher
description: "コードベースの調査・分析を行うリサーチャー"
model: fast
readonly: true
background: false
---

コードベースを調査し、関連するファイル・関数・パターンを特定してください。
変更は行わず、調査結果のみを報告してください。
```

### Cursor ルール定義の例

```yaml
# .cursor/rules/typescript.mdc
---
description: "TypeScript の strict mode 規約"
globs: ["src/**/*.ts", "src/**/*.tsx"]
alwaysApply: false
---
# TypeScript Conventions
- strict mode を常に使用
- オブジェクト型には interface を優先
```

### プロジェクト構成例（両ツール併用・最新版）

```text
project/
  AGENTS.md                          ← 両ツール共通の基本方針（オープン標準）
  CLAUDE.md                          ← Claude Code 固有の追加指示
  .cursor/
    rules/*.mdc                      ← Cursor ルール（条件付き適用）
    skills/<name>/SKILL.md           ← Cursor スキル（段階的開示）
    commands/*.md                    ← Cursor コマンド（手動起動）
    agents/*.md                      ← Cursor サブエージェント定義
    mcp.json                         ← Cursor MCP 設定
  .claude/
    skills/<name>/SKILL.md           ← Claude Code スキル（Cursor も検出可能）
    agents/*.md                      ← Claude Code サブエージェント定義
  .mcp.json                          ← Claude Code MCP 設定
```

---

## ⚠️ つまずきポイント・アンチパターン

- ==**「Cursor にサブエージェントはない」という古い情報を信じる**== → Cursor 2.4（2026年2月）で正式対応。並列実行・バックグラウンド・カスタム定義すべて可能
- ==**「Cursor でモデル別にルールを分けられる」と思い込む**== → `.cursor/rules/` はどのモデルでも共通読み込み。モデル別の自動切替機能はない
- ==**Rules と Skills の違いを理解せずに全部 Rules に書く**== → Rules は常時ロードでトークンを消費。タスク固有の知識は Skills に分離すべき（段階的開示で効率的）
- **Claude Code で Tab 補完的な体験を期待する** → CLI ベースなので IDE 統合の補完機能はない。細かいコーディングは Cursor が適切
- ==**どちらか一方だけを使おうとする**== → 排他的に選ぶ必要はなく、タスクの規模・性質に応じた併用が最も効果的
- **Skills を Cursor と Claude Code で別々に書く** → `.agents/skills/` や `.claude/skills/` は Cursor も検出可能。共通スキルは 1 箇所に統一すべき

---

## 🔁 Claude Code での活用方法

- 大規模リファクタリング、マルチファイル変更、worktree 分離が必要な作業は Claude Code で実行
- 細かいコード修正、Tab 補完を活かした高速コーディングは Cursor で実行
- スキルは `.claude/skills/` に配置すれば Cursor からも参照可能（相互運用）
- AGENTS.md をプロジェクトルートに置けば両ツール共通の基本方針として機能

例：

```text
使い分けフロー:
  1. AGENTS.md / CLAUDE.md でプロジェクト方針を定義
  2. 日常コーディング → Cursor（Tab 補完 + GUI + マルチモデル）
  3. 大規模変更・並列PR → Claude Code（/batch + worktree）
  4. 長時間リサーチ → どちらでもサブエージェントで対応可能
  5. スキルは .claude/skills/ に統一（両ツールから参照）
```

---

## 🔗 関連ノート

- [[Cursorにおけるモデル選択について]]
- [[AGENTS.mdについて]]

---

## 📝 自分の言葉で一言まとめ

> ==「Cursor 2.4 でサブエージェント・スキルが追加され、エージェント機能の差は大幅縮小。残る差分は worktree/batch/永続メモリ/権限制御（Claude Code）vs Tab補完/GUI/マルチモデル（Cursor）。スキルは相互運用可能なので、1箇所に書いて両方から使うのがベスト。」==

---

## 📌 TODO / 次に調べること

- [ ] Cursor 2.4 のサブエージェント実践例を試す（カスタムエージェント定義）
- [ ] `.claude/skills/` に配置したスキルが Cursor から正しく検出されるか検証
- [ ] Cursor の段階的開示（Progressive Disclosure）のトークン削減効果を測定
- [x] ~~Cursor Custom Modes の詳細を調査~~ → 既存機能として確認済み
- [ ] Claude Code の `/batch` コマンドの実践例を試す
- [ ] AGENTS.md + CLAUDE.md + .cursor/rules/ の三位一体運用のベストプラクティスを検証
