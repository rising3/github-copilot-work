# Testing Policy（Vue: Unit / Component / E2E）

## 目的
- UI生成の揺れ（DOM構造・分割）に強いテスト構成にする。
- テストは「契約(OpenAPI)」「状態遷移」「日本語a11y名」に寄せる。

---

## Unit（Vitest）
### 対象
- zod schema（入力・表示用の整形）
- ApiErrorの正規化（422/401/500）
- 状態遷移（reduce関数）
- DTO→ViewModel変換

---

## Component（Vue Testing Library）
### 対象
- 画面コンポーネントの状態別描画（idle/submitting/validationError/serverError）
- 主要操作（入力→送信→エラー表示）
- MSWでAPIをモック（契約に沿う）

### セレクタ（日本語固定）
- `getByRole` / `getByLabelText` を原則とする
- `data-testid` は画面仕様にある場合のみ使用

---

## E2E（Playwright）
### 対象（最小）
- 主要導線 1〜3本のみ（例：作成→一覧反映）
- 代表的な異常系 1本（例：422 or 500）

### やらない（不安定化する）
- レイアウトの細かい検証
- DOM階層に依存したセレクタ