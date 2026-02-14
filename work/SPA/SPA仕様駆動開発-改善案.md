# 1) 開発プロセス提案（おすすめの回し方）

## フェーズ0：規約・契約を先に固定（ここがブレると全部ブレる）

- UI/実装規約（Tech & Coding Rules）
- API契約（OpenAPI or JSON Schema）
- 画面ごとの状態遷移表（UI State Machine）
- テスト戦略（どの層を何でテストするか）
- 画面イメージは最後まで参考にするが、テストの基準にはしない（基準は契約・状態遷移・a11y）。

## フェーズ1：テストファーストは「ロジック→UI状態→E2E最小」に分割

- ロジック（ユースケース/バリデーション/状態遷移）をVitestでTDD
- UIは「状態ごとの表示と操作」をコンポーネントテストで固定
- E2Eは主要導線だけ（1〜3本）に絞る

# 2) SpecKit入力の具体テンプレ（これを“正”にする）

SpecKitに投入するドキュメント群を、以下の4ファイルに収束させてください（既存資料が多い場合はここへ“転記して正規化”します）。

## 2.1 01-tech-and-rules.md（生成のブレ止め）

最低限、次を明文化します。

- 採用ライブラリ固定（例）
  - Router: React Router
  - Data Fetch: TanStack Query
  - Validation: Zod
  - Form: React Hook Form
  - UI: MUI / Mantine / Headless UI など 1つに固定
- 状態の置き場所ルール
  - サーバ状態＝Query
  - 画面ローカル＝useState
  - 画面横断＝（必要なら）Zustand
- エラー表現の統一（ApiError の形、表示メッセージ規約）
- a11yとテストセレクタ規約（後述）
- フォルダ構成（後述の骨格と一致させる）

これがないと、AIは毎回別のスタック/構造で生成しがちです。

## 2.2 02-api-contract.(yaml|json)（OpenAPI推奨）

- request/response
- validation error（422など）のレスポンス構造
- auth error（401）
- not found（404）
- conflict（409）
- server error（500）

> **重要：** モック（MSW）はこの契約に従う、を規約化します。

## 2.3 03-feature-spec.md（機能仕様：UIより“状態と振る舞い”中心）

- ユーザーストーリー
- 受け入れ条件（Given/When/Then）
- 例外系（必ず）
- 権限/遷移条件
- 保存後の挙動（再フェッチする、楽観更新する、トースト出す等）

## 2.4 04-screen-state-machines.md（画面ごとの状態遷移表）

各画面について、最低限これを表で定義します。

- **State:** initial / loading / success / empty / validationError / serverError
- **Event:** mount / submit / retry / changeFilter ...
- **Transition**
- **UI:** 表示要素、ボタン活性、メッセージ
- **Side effect:** fetchX, mutateY, navigateZ

> ここがあるとテストがDOM依存にならず、「状態が合っているか」を検証できます。UI生成がブレてもテストが壊れにくくなります。

# 3) テスト設計提案（“テストファースト”を成立させる分解）

## 3.1 テストピラミッド（推奨配分）

- Unit（Vitest）：70%
  - ドメイン/ユースケース/バリデーション/状態遷移
- Component（Testing Library or Playwright CT）：25%
  - 画面の状態別表示、主要操作
- E2E（Playwright）：5%
  - 主要導線1〜3本（配線確認）

> E2Eを厚くすると、AI生成の揺れに最も弱い層が肥大化して破綻しやすいです。

## 3.2 “UIが変わっても壊れない”E2Eのルール

- 取得は原則 getByRole / getByLabel / getByText（a11y基準）
- data-testid は最小限、かつ仕様書に「安定ID一覧」を書く<BR>
  例：送信ボタン、ダイアログ確定ボタン、トースト領域 など
- E2Eで検証するのは
  - ある入力で成功する
  - ある入力でエラーになる
  - 成功後に一覧へ反映される<BR>
    まで。細かいレイアウトや文言差分は見ない（必要ならコンポーネントテストへ）

## 3.3 MSW（APIモック）運用ルール

- “画面ごとに好きにモックを書く”を禁止
- 契約に沿ったハンドラを共通化
- 成功・失敗・遅延を切り替える仕組みを用意（E2Eの安定化）

# 4) 生成を安定させる「実装骨格」提案（ここは先に人間が固定）

AIに自由に作らせないで、まず以下をリポジトリに用意し、以降はそれに沿わせます。

## 4.1 フォルダ構成（例）

```
src
  app：ルーティング、Providers、App起動
  features
    <feature>
      api：API呼び出し（Query/Mutation）
      model：型、Zod schema、状態遷移
      ui：ページ・コンポーネント
      __tests__/：
  unit
    component
  shared
    api：fetch wrapper、ApiError、共通client
    ui：Button, Dialog, FormFieldなど
    test：MSW setup、test utilities
```

## 4.2 型・エラー・バリデーションを最初に“共通化”

- ApiError（共通形）
- parseApiError()（共通）
- Zod schema（入力/レスポンス）
- Query key命名規則

> これがないと、AIが画面ごとに勝手なエラー処理や型を作ってブレます。

## 4.3 “状態遷移は純粋関数”で固定（テストファーストの要）

**例：** `reduceScreenState(state, event) -> newState` のような形を規約にすると、

- unitテストが書きやすい
- UIはそのstateを描画するだけになり、生成が安定
- E2Eは状態をなぞるだけで済む

# 5) SpecKit + Copilot運用ルール（生成のブレを減らす手順）

# 5.1 1機能の実装順（テンプレ）

- **契約：** APIスキーマ確認/追加（必要なら）
- **状態遷移表：** 画面のstate/events確定
- **Unitテスト：** 状態遷移・バリデーション（先に）
- **MSWハンドラ：** 成功/失敗を用意
- **Componentテスト：** stateごとの表示と操作（先に）
- **実装：** UIはstateとqueryを配線するだけ
- **E2E：** 主要導線の最小確認

## 5.2 “AIに渡す指示”の型を固定

- 「この機能はこのフォルダ構成に追加」
- 「状態遷移関数のシグネチャはこれ」
- 「テストはこの観点（state table）を網羅」
- 「E2Eではこの安定ID/role/nameを使う」

> 同じ形の指示を繰り返すほど、生成が安定します。毎回違う言い方をするとブレます。

# 6) まずやるべき最小セット（次の一手）

次の3点を作る（または既存資料から“正規化して作り直す”）のが最優先です。

- **Tech & Coding Rules（規約）：** 採用ライブラリ・状態管理・a11y/selector・フォルダ構成
- **API Contract：** 最低でも主要エンドポイントの成功/失敗スキーマ
- **Screen State Machines：** 主要画面1枚だけでも（例：一覧 + 作成フォーム）

これが揃うと、テストファーストが「E2E先行」ではなく「契約/状態先行」に変わり、画面生成の揺れに強くなります。
