# 画面状態遷移テンプレ（UIテスト安定化の核）

## 画面
- 画面名：
- パス：
- 見出し（日本語・固定）：

## 状態（State）
- idle：初期表示/入力可能
- loading：初期データ取得中（必要な場合）
- submitting：送信中（ボタン無効＋スピナー）
- success：成功（トースト＋遷移 or 表示更新）
- validationError：入力エラー（フィールドに日本語エラー）
- serverError：サーバエラー（日本語バナー＋再試行）

## イベント（Event）
- MOUNT
- CHANGE_FIELD(field, value)
- SUBMIT
- API_SUCCESS(payload)
- API_VALIDATION_ERROR(fieldErrors)
- API_SERVER_ERROR(message)
- RETRY

## 遷移表
| 現状態 | イベント | 次状態 | UI期待（日本語文言） | 副作用 |
|---|---|---|---|---|
| idle | SUBMIT | submitting | 「保存」無効＋スピナー | create/update API |
| submitting | API_VALIDATION_ERROR | validationError | 「入力内容を確認してください」 | - |
| submitting | API_SERVER_ERROR | serverError | 「サーバーで問題が発生しました」＋「再試行」 | - |
| submitting | API_SUCCESS | success | 「保存しました」 | invalidateQueries / navigate |

## テスト観点（この表を必ず網羅）
- submitting時に「保存」ボタンが無効になる
- 422で各フィールドに日本語エラーが出る
- 500でバナーと「再試行」が出る
- successでトースト表示＋遷移/更新