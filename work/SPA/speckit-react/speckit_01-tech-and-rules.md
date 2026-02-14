# Tech & Coding Rules (SPA / 日本語UI固定)

## 目的
- 仕様駆動開発（SpecKit + Copilot）で、SPAの画面コード生成を安定させる。
- テストファーストを成立させるため、UIテストはDOM構造ではなく **a11y名（日本語UI文言）と状態** に寄せる。

---

## 技術スタック（固定）
- Vite + React + TypeScript
- Tailwind CSS + DaisyUI
- React Router（画面遷移）
- TanStack Query（サーバ状態の唯一の置き場所）
- React Hook Form + Zod（フォーム/入力バリデーション）
- MSW（APIモック：Unit/CT/E2Eで共有）
- Playwright（Component Testing + E2E）
- Vitest（Unit）

---

## UI文言（日本語固定ルール）
### ルール
- 画面上に表示する文言（見出し、ボタン、ラベル、バリデーション、トースト等）は **日本語を正とする**
- テスト（Playwright）は原則として **日本語のアクセシブルネーム** で要素を取得する
- 仕様書に記載した日本語文言は、実装で勝手に言い換えない

### ボタン文言（標準語彙：例）
- 作成 / 登録 / 保存 / 更新 / 削除 / キャンセル / 閉じる / 戻る / 次へ / 検索 / 再試行

> 各画面で語彙を追加する場合は、機能仕様に「UI文言一覧」として追記する。

---

## アクセシビリティ & セレクタ規約（重要）
### 原則
- Playwrightのセレクタは以下の優先順：
  1. `getByRole(..., { name: '日本語' })`
  2. `getByLabel('日本語ラベル')`
  3. `getByText('日本語テキスト')`（表示文言が安定しているもののみ）
- `data-testid` は **最小限**。必要な場合のみ画面���様でIDを固定する。

### フォーム規約（必須）
- すべての入力は `<label htmlFor>` + `id` で関連付ける
- 必須項目は日本語で「必須」を表示（例：ラベル右にバッジ）
- バリデーションメッセージは日本語（例：「メールアドレスを入力してください」）

### 例：推奨セレクタ
- ボタン：`page.getByRole('button', { name: '保存' })`
- 入力：`page.getByLabel('メールアドレス')`
- ダイアログ：`page.getByRole('dialog', { name: '削除の確認' })`

---

## フォルダ構成（固定）
- `src/app/`：Router、Providers、AppShell
- `src/shared/`
  - `api/`：apiClient、ApiError、OpenAPI型、queryKeyヘルパ
  - `ui/`：共通UI（Alert、Modal、Toast、FormField等）
  - `test/`：MSW server/handlers、test utilities
- `src/features/<feature>/`
  - `model/`：Zod schema、状態遷移（純粋関数）、DTO変換
  - `api/`：TanStack Query hooks（query/mutation）
  - `ui/`：画面/コンポーネント
  - `__tests__/`：unit + CT

---

## TanStack Query 運用規約
- サーバデータは TanStack Query のみで管理（グローバルstoreに置かない）
- Query Key は feature単位で集中管理
- Mutation 後は `invalidateQueries` か `setQueryData` を必ず明示

---

## エラー処理規約（表示と挙動を固定）
- 422（入力エラー）：
  - フィールドごとに日本語エラーメッセージを表示
  - 入力値は保持
- 401（認証）：
  - （アプリ方針に従い）ログイン画面へ遷移 or 「セッションが切れました」を表示
- 500（サーバ）：
  - 画面上部に Alert（日本語）＋「再試行」ボタン
  - 再試行で同じAPIを再実行

---

## テスト方針（テストファースト）
- Unit（Vitest）：model（状態遷移、Zod、変換、ApiErrorマップ）
- Component（Playwright CT）：状態別UI（idle/loading/error/success）と主要操作
- E2E（Playwright）：主要導線1〜3本のみ（配線確認）