---
type: learning-note
domain: claude-code
topic: Cursor と Claude Code の違い
source_type: experiment
source:
  title: Claude Code との会話による比較検証
  author:
  url:
  date: 2026-03-04
created: 2026-03-04
updated: 2026-03-04
tags:
  - claude-code
  - cursor
  - learning
  - comparison
  - agent
---

# Cursor と Claude Code の違い

## 🧭 概要（30秒で思い出す）

- Cursor は GUI（VS Code フォーク）× マルチモデル、Claude Code は CLI × Claude 専用
- ==エージェントの自律性・拡張性では Claude Code が明確に優位==（サブエージェント、スキル、永続メモリ、並列実行）
- ==日常的なコーディング支援（Tab 補完・GUI 操作）では Cursor が便利。併用が最も効果的==

### 一目でわかるエージェント機能差

| 機能           | Claude Code                            | Cursor Agent  |
| ------------ | -------------------------------------- | ------------- |
| サブエージェント並列起動 | Explore / Plan / General-purpose を同時起動 | **非対応**（逐次処理） |
| バックグラウンド実行   | 非同期で並列実行可能                             | **非対応**       |
| ワークツリー分離     | git worktree で安全に並列作業                  | **非対応**       |
| スキル（拡張コマンド）  | カスタム定義可能（ツール制限・モデル指定・引数展開）             | プロンプトテンプレートのみ |
| 自律的なタスク分解    | TodoList で自己管理・進捗追跡                    | 限定的           |
| 永続メモリ        | `.claude/` 配下にセッション間で自動蓄積              | **非対応**       |
| 大規模並列変更      | `/batch` で worktree 並列 + 自動 PR         | **非対応**       |
| 権限の細粒度制御     | ツール・パス・ドメイン単位で Allow/Deny              | エディタの信頼設定に依存  |

---

## 📚 情報ソース

- 種別：実験 / Claude Code との会話による比較検証
- タイトル：Claude Code との会話（2026-03-04）
- メモ：公式ドキュメント・実際の機能を元に両ツールを体系的に比較

---

## 🧩 要点まとめ

### 基本情報

| 項目 | Cursor | Claude Code |
|------|--------|-------------|
| 形態 | GUI（VS Code フォーク） | CLI（ターミナル + エディタ拡張） |
| 対応モデル | Claude, GPT-4o, Gemini 等 | Claude 専用（Opus / Sonnet / Haiku） |
| 主な操作場所 | エディタ内チャットパネル | ターミナル / VS Code・JetBrains 拡張 |
| 料金体系 | Pro $20/月, Business $40/月 等 | Max $100/月 または API 従量課金 |

### ルール／プロジェクト指示

| 観点 | Cursor | Claude Code |
|------|--------|-------------|
| ファイル形式 | `.mdc`（独自形式 + YAML frontmatter） | `.md`（標準 Markdown） |
| 設定場所 | `.cursor/rules/*.mdc` | `CLAUDE.md` / `.claude/rules/*.md` |
| 適用条件 | glob パターン / description / always | パスベースの自動ロード + paths 指定 |
| インポート機能 | ファイル参照は可能 | `@path` 構文で再帰インポート（最大5ホップ） |
| 管理者制御 | Team Dashboard | Managed Policy（OS レベル） |
| オープン標準 | AGENTS.md 対応 | CLAUDE.md 独自形式 |

Cursor の 4 つのルールタイプ: Always Apply / Auto Attached（globs） / Agent Requested（description） / Manual（@呼び出し）

### スキル／スラッシュコマンド

| 観点 | Cursor Commands | Claude Code Skills |
|------|----------------|-------------------|
| 本質 | プロンプトテンプレート | ==ワークフロー定義（ツール・モデル・権限を含む）== |
| 設定場所 | `.cursor/commands/*.md` | `.claude/skills/<name>/SKILL.md` |
| 実行制御 | なし | ツール制限、モデル指定、サブエージェント指定 |
| 自動呼び出し | なし（手動のみ） | ==AI が必要に応じて自動呼び出し可能== |
| 引数展開 | 追加テキストとして渡す | `$ARGUMENTS` / `$0` で位置引数対応 |
| 並列実行 | なし | `/batch` でワークツリー並列実行 |

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
> ==両ツールは競合ではなく補完関係==。エージェントの自律性は Claude Code、GUI の利便性は Cursor。

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

### Claude Code 固有の強み: サブエージェント並列処理

```text
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

---

## ⚠️ つまずきポイント・アンチパターン

- ==**「Cursor でモデル別にルールを分けられる」と思い込む**== → `.cursor/rules/` はどのモデルでも共通読み込み。モデル別の自動切替機能はない
- ==**Cursor で複雑なエージェント的タスクを無理にやろうとする**== → サブエージェント・並列実行がないため非効率。大規模タスクは Claude Code に切り替えるべき
- **Claude Code で Tab 補完的な体験を期待する** → CLI ベースなので IDE 統合の補完機能はない。細かいコーディングは Cursor が適切
- ==**どちらか一方だけを使おうとする**== → 排他的に選ぶ必要はなく、タスクの規模・性質に応じた併用が最も効果的

---

## 🔁 Claude Code での活用方法

- 大規模リファクタリング、マルチファイル変更は Claude Code で実行
- 細かいコード修正、Tab 補完を活かした高速コーディングは Cursor で実行
- プロジェクトルールは `CLAUDE.md`（Claude Code 用）と `.cursor/rules/`（Cursor 用）を併設し、両方で開発可能にしておく

例：

```text
プロジェクト構成:
  CLAUDE.md              ← Claude Code 用プロジェクト指示
  .cursor/rules/*.mdc    ← Cursor 用プロジェクトルール
  .mcp.json              ← MCP サーバー設定（両方で共有可能）
```

---

## 🔗 関連ノート

- [[cursorにおけるモデル選択について]]

---

## 📝 自分の言葉で一言まとめ

> ==「Cursor は GUI × マルチモデルの日常開発ツール、Claude Code は Claude 専用の自律型エージェント。大きな仕事は Claude Code、小さな仕事は Cursor で使い分けるのが最強。」==

---

## 📌 TODO / 次に調べること

- [ ] Cursor Custom Modes の詳細（ツール制限・カスタム指示の設定方法）を調査
- [ ] Claude Code の `/batch` コマンドの実践例を試す
- [ ] `.cursor/rules/` と `CLAUDE.md` を同一プロジェクトで併設した場合のベストプラクティスを検証
- [ ] Claude Code のカスタムサブエージェント（`.claude/agents/`）の定義方法を調査
