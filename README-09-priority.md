# GitHub Copilot ファイル参照の優先順位

## ✅ まとめ

| 質問 | 回答 |
| --- | --- |
| 最優先されるのは？ | 作業中のファイルに最も近いディレクトリの指示ファイル |
| copilot-instructions.md vs AGENTS.md | copilot-instructions.mdが基準、AGENTS.mdは「近さ」で優先 |
| 複数該当する場合 | マージされるが、より具体的な指示が優先 |
| 推奨戦略 | copilot-instructions.md（全体）+ instructions/.instructions.md（種別）+ agents/.md（役割） |

> 重要: ファイルは短く明確に保ち、重要な指示は先頭に配置することで、コンテキスト切り捨てを回避できます。

## 💡 推奨構成（優先順位を考慮）

```markdown
.github/
├── copilot-instructions.md           # 全体のベースライン
├── instructions/
│   ├── api. instructions.md          # applyTo: "src/app/api/**/*.ts"
│   ├── component.instructions.md     # applyTo: "src/components/**/*.tsx"
│   └── test.instructions.md          # applyTo: "**/*.test.ts"
└── agents/
    ├── frontend-specialist.md        # @frontend エージェント
    ├── backend-specialist.md         # @backend エージェント
    └── test-specialist.md            # @test エージェント
```

**メリット:**

- copilot-instructions.md: 技術スタック・コマンド（全体共通）
- *.instructions.md: ファイル種別ごとの具体的ルール（高優先度）
- agents/*. md: 役割別の専門知識（エージェント指定時）

## 📊 優先順位マップ（高→低）

``` markdown
┌──────────────────────────────────────────────────────┐
│ 1. 個人設定（User-level）                             │ ← 最優先
│    GitHub. com の設定 > IDE内の個人設定                │
└──────────────────────────────────────────────────────┘
                     ↓ オーバーライド
┌──────────────────────────────────────────────────────┐
│ 2. ディレクトリ固有の指示（最も近い）                   │
│    . github/instructions/[NAME].instructions.md      │
│    （applyToパターンに一致した場合）                   │
└──────────────────────────────────────────────────────┘
                     ↓ オーバーライド
┌──────────────────────────────────────────────────────┐
│ 3. カスタムエージェント（作業中のファイルに最も近い）     │
│    .github/agents/[AGENT-NAME].md                    │
│    （エージェント明示的に指定時）                       │
└──────────────────────────────────────────────────────┘
                     ↓ オーバーライド
┌──────────────────────────────────────────────────────┐
│ 4. リポジトリ全体の指示                                │
│    .github/copilot-instructions.md                   │
└──────────────────────────────────────────────────────┘
                     ↓ オーバーライド
┌─────────────────────────────────────────────────────┐
│ 5. Organization/Enterprise設定                       │
│    .github-private/copilot-instructions.md          │
│    . github-private/agents/[AGENT-NAME].md          │
└─────────────────────────────────────────────────────┘
```

## 🔍 詳細な優先順位ルール

### 1. 個人設定 > リポジトリ設定 > 組織設定

```
個人のカスタム指示
  ↓ (マージ＆オーバーライド)
リポジトリのカスタム指示
  ↓ (マージ＆オーバーライド)
組織全体のカスタム指示
```

### 2. ディレクトリの近さ（Proximity Rule）

```
プロジェクト構造: 
├── AGENTS.md                    # ← 優先度:  低
├── src/
│   ├── AGENTS.md                # ← 優先度: 中
│   └── components/
│       ├── AGENTS.md            # ← 優先度: 高（最も近い）
│       └── Button.tsx           # ← 作業中のファイル
```

例: src/components/Button.tsx を編集中 → src/components/AGENTS.md が最優先で参照される

### 3. ファイルパターンマッチ（applyTo）

````markdown
---
applyTo: "src/**/*.test.ts"
---
# テストファイル専用の指示
すべてのテストはJest + React Testing Libraryで書くこと
````

## 📂 ファイル種別ごとの役割と優先順位

| ファイル | 配置場所 | スコープ | 優先度 | 用途 |
| --- | --- | --- | --- | --- |
| copilot-instructions.md | .github/ | リポジトリ全体 | 基準 | デフォルトルール・技術スタック |
| [NAME].instructions.md | .github/instructions/ | パターンマッチファイル | 高 | ファイル種別ごとの特化ルール |
| [AGENT-NAME].md | .github/agents/ | エージェント指定時 | 中〜高 | 役割別の専門知識 |
| AGENTS.md | 任意のディレクトリ | ディレクトリ配下 | ディレクトリの近さ依存 | 汎用エージェント指示（Cursor等でも使用） |

## 🎯 実践的な優先順位の例

### シナリオ: src/components/todo/TodoItem.tsx を編集中

```
プロジェクト構造: 
├── . github/
│   ├── copilot-instructions.md             # ① 全体ルール
│   ├── instructions/
│   │   └── react-component.instructions.md # ② applyTo:  "src/**/*.tsx"
│   └── agents/
│       └── frontend-specialist.md          # ③ @frontendエージェント指定時
├── AGENTS.md                               # ④ ルートレベル
└── src/
    ├── AGENTS.md                           # ⑤ srcレベル
    └── components/
        ├── AGENTS.md                       # ⑥ 最も近い（最優先）
        └── todo/
            └── TodoItem.tsx                # ← 作業中
```

### 適用される優先順位:

| ケース | 適用されるファイル（高→低） |
| --- | --- |
| 通常編集 | ⑥ → ① |
| @frontend指定 | ⑥ → ③ → ① |
| applyToマッチ | ⑥ → ② → ① |
| 全部該当 | ⑥ → ② → ③ → ① |

## ⚠️ 重要な注意点

### 1. マージ vs オーバーライド

- 基本的に指示はマージされる（複数のファイルの内容が組み合わされる）
- 同じ項目（例: コーディングスタイル）が重複する場合はより具体的な指示が優先

```
# . github/copilot-instructions.md
- インデント: 2スペース

# .github/instructions/python.instructions.md
- インデント: 4スペース  ← Pythonファイルではこちらが優先
```

### 2. コンテキストウィンドウの制限

- Copilotが一度に読み込めるトークン数には上限がある
- ファイルが長すぎると、重要な指示が切り捨てられる可能性

**推奨:**

- 各ファイルは簡潔に（目安: 500行以下）
- 重要な指示はファイルの先頭に配置

### 3. カスタムエージェント vs AGENTS.md

| 項目 | .github/agents/[NAME].md | AGENTS.md |
| --- | --- | --- |
| GitHub Copilot | ✅ 正式サポート | ✅ サポート |
| Cursor/Windsurf | ⚠️ 読まれない可能性 | ✅ サポート |
| 配置 | .github/agents/ のみ | どのディレクトリでもOK |
| 用途 | GitHub Copilot専用の高度なエージェント | 汎用的なAI向け指示 |