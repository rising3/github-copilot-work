# Testing Policy（Unit / Playwright CT / E2E）

## 目的
- UI生成の揺れ（DOM構造やコンポーネント分割）に強いテスト構成にする。
- テストは「契約(OpenAPI)」「状態遷移」「日本語a11y名」に寄せる。

---

## Unit（Vitest）
### 対象
- Zod schema（フロント入力）
- ApiError正規化（422/401/500）
- state machine（reduce関数）
- DTO→ViewModel変換

### 禁止
- Reactコンポーネントの描画テストはUnitでやらない（CTに寄せる）

---

## Component Testing（Playwright CT）
### 対象
- 画面コンポーネントの状態別描画（idle/submitting/validationError/serverError）
- 主要操作（入力→送信→エラー表示）
- MSWでAPIをモック（契約に沿う）

### セレクタ
- `getByRole` / `getByLabel` を原則とする（日本語固定）

---

## E2E（Playwright）
### 対象（最小）
- 主要導線 1〜3本のみ
  - 例：作成→一覧反映
  - 例：入力エラー表示（422）を1本だけ
- それ以外の詳細はCT/Unitに移す

### 失敗しやすいのでやらない
- レイアウトの細かい検証
- DOM階層に依存したセレクタ