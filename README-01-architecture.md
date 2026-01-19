# 設計判断のドキュメント戦略

## 💡 ベストプラクティス

- architecture.md: 「今の状態」を記述、500行以内を目標
- decisions/*. md: 「なぜそうなったか」を記録、1ファイル = 1決定
- ADRは不変: 決定変更時は新ファイル作成（古いファイルはステータス変更のみ）
- Copilot対策: 重要な情報はファイル冒頭に配置
- 定期レビュー: 四半期ごとにADRステータスを見直し

## 📋結論(推奨構成)

| ファイル | 内容 | 更新頻度 | ファイル長 |
| --- | --- | --- | --- |
| docs/architecture.md | 現在のアーキテクチャ全体像（最新状態のスナップショット） | 低（大きな変更時のみ） | 中〜長（OK） |
| docs/decisions/README.md | ADR一覧とインデックス（過去の決定履歴へのポインタ） | 高（決定ごと） | 短（常に短い） |
| docs/decisions/NNNN-title.md | 個別の設計判断（1ファイル = 1決定） | 不変（作成後は編集しない） | 短（500行以下） |

## 📂 推奨ディレクトリ構成

``` markdown
docs/
├── README.md                      # ドキュメント全体の索引
├── introduction.md                # プロジェクト概要
├── architecture.md                # 現在のアーキテクチャ（Living Document）
├── decisions/                     # ADRディレクトリ
│   ├── README.md                  # ADR一覧とテンプレート
│   ├── 0001-use-nextjs-app-router.md
│   ├── 0002-use-sqlite.md
│   ├── 0003-no-orm.md
│   ├── 0004-use-jest.md
│   ├── 0005-use-zod.md
│   └── template.md                # 新規ADR作成用テンプレート
└── guides/                        # 運用ガイド
    ├── setup.md
    └── deployment.md
```

## 🎯 使い分けの原則

### docs/architecture.md：「今のシステムはこうなっている」

**用途：**

- 現在のアーキテクチャ全体像
- システム図・データフロー
- 技術選定の現状まとめ
- 新規メンバーのオンボーディング資料

**特徴：**

- Living Document（生きたドキュメント）
- 定期的に更新される
- 「なぜこうなったか」の詳細は書かない（ADRへ参照）

**サンプル構成：**

``` markdown
# Architecture

## 現在のシステム構成
[システム図]

## 技術スタック
- Next.js 15 (App Router) → 詳細:  [ADR-0001](./decisions/0001-use-nextjs-app-router.md)
- SQLite (ORM不使用) → 詳細: [ADR-0003](./decisions/0003-no-orm.md)

## データフロー
[フロー図]

## セキュリティ設計
[現在の対策]
```

---

### docs/decisions/NNNN-title.md：「なぜその判断をしたか」

**用途：**

- 1つの重要な技術判断を記録（1ファイル = 1決定）
- 判断時のコンテキスト・代替案・トレードオフ
- 将来の見直し時の参考資料

**特徴：**

- Immutable（不変）：作成後は編集しない
- 決定が覆された場合は新しいADRで上書き（古いADRはステータスを"Superseded"に）
- 連番管理（0001, 0002, ...）
- 業界標準テンプレート（MADR形式）：

``` markdown
# ADR-0003: SQLiteを直接使用（ORM不使用）

## ステータス
✅ Accepted (2026-01-18)

## コンテキスト
TodoListアプリのデータ永続化方法を決定する必要がある。
- シンプルなCRUD操作のみ
- 学習用途でSQL理解を深めたい
- 初期セットアップを最小限にしたい

## 考慮した選択肢

### 選択肢1: Prisma（ORM）
**メリット**：
- 型安全なクエリ
- マイグレーション管理が楽
- N+1問題の自動最適化

**デメリット**：
- 学習コストが高い
- 生成コードが多い（理解しづらい）
- SQLの学習機会を失う

### 選択肢2: better-sqlite3（直接SQL）
**メリット**：
- SQLを直接書くため透明性が高い
- オーバーヘッドが少ない
- クエリの最適化を自分で制御できる

**デメリット**：
- 型安全性は手動で確保（TypeScript interfaceが必要）
- マイグレーション管理を自作

### 選択肢3: Drizzle ORM
**メリット**：
- 軽量でSQLライクな構文
- TypeScript型推論が強力

**デメリット**：
- エコシステムがPrismaより小さい
- 学習リソースが少ない

## 決定
**選択肢2を採用**:  better-sqlite3で直接SQL

**理由**：
1. **学習目標との一致**:  SQLスキル向上が目的
2. **透明性**: どんなクエリが実行されるか明確
3. **シンプルさ**: ORMの抽象化レイヤーが不要

## 結果/影響

### ポジティブ
- SQLクエリが可視化され、パフォーマンス最適化しやすい
- 依存関係が少ない（better-sqlite3のみ）
- TypeScript interfaceでも十分な型安全性

### ネガティブ
- マイグレーション管理を自作する必要がある
- Zodスキーマとの二重定義が発生

### リスクと軽減策
**リスク**:  本番環境移行時の工数増加  
**軽減策**: 将来PostgreSQL移行時はDrizzle ORMを検討（SQLライクで移行が容易）

## 関連
- [ADR-0001: Next.js App Router��用](./0001-use-nextjs-app-router.md)
- [ADR-0005:  Zodによるバリデーション](./0005-use-zod.md)

## メタデータ
- **作成者**: @rising3
- **作成日**: 2026-01-18
- **レビュワー**: -
- **次回見直し時期**: 2026-06 (本番環境移行時)
```

## 📝docs/decisions/README.md のサンプル

```` markdown
# Architecture Decision Records (ADR)

このディレクトリには、TodoListアプリの重要な技術判断を記録します。

## ADR一覧

| ID | タイトル | ステータス | 日付 |
|----|---------|-----------|------|
| [0001](./0001-use-nextjs-app-router.md) | Next.js App Router採用 | ✅ Accepted | 2026-01-15 |
| [0002](./0002-use-sqlite. md) | SQLite採用 | ✅ Accepted | 2026-01-16 |
| [0003](./0003-no-orm. md) | ORM不使用 | ✅ Accepted | 2026-01-18 |
| [0004](./0004-use-jest. md) | Jestをテストフレームワークに採用 | ✅ Accepted | 2026-01-18 |
| [0005](./0005-use-zod.md) | Zodによるバリデーション | ✅ Accepted | 2026-01-18 |

## ステータス定義

- ✅ **Accepted**: 採用決定
- ⏳ **Proposed**: 提案中（レビュー待ち）
- ❌ **Rejected**: 却下
- ⚠️ **Deprecated**: 非推奨（後継ADRあり）
- 🔄 **Superseded**: 上書きされた（後継ADRへリンク）

## ADR作成ルール

1. **1ファイル = 1決定**
2. **連番を使用**（0001, 0002, ...）
3. **テンプレートに従う**（[template.md](./template.md)）
4. **作成後は編集しない**（決定変更時は新ADRを作成）

## テンプレート

新しいADRを作成する際は、[template.md](./template.md) をコピーしてください。

```bash
# 新規ADR作成
cp docs/decisions/template.md docs/decisions/0006-new-decision.md
```

## 関連ドキュメント

- [アーキテクチャ全体像](../architecture.md) - 現在のシステム構成
- [プロジェクト概要](../introduction.md) - ビジネスコンテキスト

````

## ⚠️ ファイル長の問題と対策

### 問題1: architecture.md が長くなりすぎる

**症状：**

- 1000行超えでスクロールが大変
- GitHub Copilotのコンテキストウィンドウからあふれる

**対策：**

#### A. セクション分割（推奨）

```
docs/
├── architecture/
│   ├── README. md              # 全体像とナビゲーション
│   ├── system-overview.md     # システム構成図
│   ├── data-flow.md           # データフロー
│   ├── security. md            # セキュリティ設計
│   ├── performance. md         # パフォーマンス戦略
│   └── deployment.md          # デプロイ戦略
```

#### B. 階層化（中規模プロジェクト向け）

```
docs/
├── architecture. md            # 高レベル概要（500行以内）
├── architecture/
│   ├── frontend.md            # フロントエンド詳細
│   ├── backend.md             # バックエンド詳細
│   └── database.md            # データベース詳細
```

### 問題2: ADRが増えすぎて管理困難

**症状：**

- 50個以上のADRファイル
- どれが有効か不明

**対策：**

#### A. カテゴリ分類

```
docs/decisions/
├── README.md
├── frontend/
│   ├── 0001-use-nextjs. md
│   └── 0004-use-tailwind.md
├── backend/
│   ├── 0002-use-sqlite.md
│   └── 0003-no-orm.md
└── testing/
    └── 0005-use-jest.md
```

---

#### B. ステータスフィルタ（README.mdで実装）

``` markdown
## 有効なADR（Accepted）

| ID | タイトル | 日付 |
|----|---------|------|
| [0001](./0001-use-nextjs.md) | Next.js採用 | 2026-01-15 |

<details>
<summary>非推奨のADR（Deprecated/Superseded）</summary>

| ID | タイトル | ステータス | 後継ADR |
|----|---------|-----------|---------|
| [0010](./0010-use-webpack.md) | Webpack使用 | 🔄 Superseded | [0015](./0015-use-vite.md) |

</details>
```

