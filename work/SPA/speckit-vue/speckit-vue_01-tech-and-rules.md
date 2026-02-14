# Tech & Coding Rules (Vue SPA / 日本語UI固定)

## 目的
- 仕様駆動開発（SpecKit + Copilot）で Vue SPA を安定して実装する。
- UIの揺れに強いように、テストは DOM構造ではなく **日本語のアクセシブルネーム** と **状態遷移** に寄せる。

---

## 技術スタック（固定）
- Vite + Vue 3 + TypeScript（Composition APIのみ）
- Tailwind CSS + DaisyUI
- Vue Router
- TanStack Query (Vue): `@tanstack/vue-query`（サーバ状態の唯一の置き場所）
- フォーム: `vee-validate` + `@vee-validate/zod` + `zod`
- APIモック: MSW（Unit/Component/E2Eで共有）
- Unit: Vitest
- Component: Vue Testing Library（主戦力）
- E2E: Playwright（最小）

---

## UI文言（日本語固定ルール）
### ルール
- 見出し/ラベル/ボタン/エラーメッセージ/トースト等のUI文言は **日本語を正とする**
- 仕様書に書いた日本語文言は実装で勝手に言い換えない
- テストは原則として日本語のa11y名で要素を取得する（`getByRole`, `getByLabelText`）

### ボタン標準語彙（例）
- 作成 / 登録 / 保存 / 更新 / 削除 / キャンセル / 閉じる / 戻る / 次へ / 検索 / 再試行

---

## アクセシビリティ & セレクタ規約
### 原則
- Componentテスト（Vue Testing Library）は以下の優先順で取得する：
  1. `getByRole(..., { name: '日本語' })`
  2. `getByLabelText('日本語ラベル')`
  3. `getByText('日本語テキスト')`（表示文言が安定している場合のみ）
- `data-testid` は最小限。必要な場合は画面仕様にIDを固定して記載する。

### フォーム規約（必須）
- すべての入力に日本語ラベルを付け、入力要素と関連付ける
  - label + `for` / input `id` を基本とする（DaisyUIの見た目と両立）
- 必須項目は「必須」を日本語表示
- バリデーションメッセージは日本語で統一

---

## Vue実装ルール（生成のブレ止め）
- Options APIは禁止、Composition APIのみ
- `script setup` を標準（例外は仕様に明記）
- 状態の置き場所：
  - サーバ状態：`@tanstack/vue-query`（必須）
  - ローカルUI状態：`ref`/`reactive`
  - グローバル状態管理（Pinia等）は使わない（禁止）
- フォーム：
  - `vee-validate` を必須採用
  - schemaは `zod` を正とし、`toTypedSchema` 経由で接続する
- API型：
  - OpenAPIから生成された型（または手書き型）を単一ソースとして利用
  - エラーは `ApiError` に正規化して扱う

---

## フォルダ構成（固定）
- `src/app/`：router、providers、AppShell
- `src/shared/`
  - `api/`：apiClient、ApiError、OpenAPI型、queryKey helper
  - `ui/`：共通UI（Alert/Modal/Toast/FormField等）
  - `validation/`：zod共通schemaやヘルパ（必要なら）
  - `test/`：MSW setup、test utilities
- `src/features/<feature>/`
  - `model/`：zod schema、状態遷移（純粋関数）、DTO変換
  - `api/`：vue-query hooks（query/mutation composables）
  - `ui/`：Vueコンポーネント（ページ/部品）
  - `__tests__/`：unit + component

---

## TanStack Query 運用規約（Vue）
- サーバデータは `useQuery/useMutation` のみで管理
- Query Key は feature単位で集中管理
- mutation後は `invalidateQueries` か `setQueryData` を必ず明示

---

## エラー処理規約（表示と挙動）
- 422（入力エラー）：
  - フィールドごとに日本語エラー表示
  - 入力値は保持
- 401（認証）：
  - 方針に従いログイン遷移 or 「セッションが切れました」
- 500（サーバ）：
  - 画面上部に日本語のAlert＋「再試行」ボタン
  - 再試行で同APIを再実行

---

## テスト方針（テストファースト）
- Unit（Vitest）：
  - zod schema
  - ApiError正規化
  - 状態遷移（reduce関数）
  - DTO→ViewModel変換
- Component（Vue Testing Library）：
  - 状態別UI（idle/submitting/validationError/serverError）
  - 主要操作（入力→送信→エラー表示）
  - MSWでAPIをモック（契約に沿う）
- E2E（Playwright）：
  - 主要導線1〜3本のみ（配線確認）