# TodoList API Documentation

## 📋 概要

TodoList APIは、タスク管理機能を提供するREST APIです。

- **Base URL**: `http://localhost:3000/api`
- **Version**: `v1`
- **Protocol**:  HTTPS (本番環境)
- **Data Format**: JSON

---

## 🚀 クイックスタート

### 1. サーバー起動

```bash
npm run dev
```

### 2. Todoを取得

```bash
curl http://localhost:3000/api/todos
```

### 3. Todoを作成

```bash
curl -X POST http://localhost:3000/api/todos \
  -H "Content-Type: application/json" \
  -d '{"title":  "Buy groceries"}'
```

---

## 🔐 認証

**現在のバージョン（MVP）では認証は実装されていません。**

### 将来の認証方式（予定）

Phase 2で以下を実装予定：

- **方式**:  Bearer Token (JWT)
- **ヘッダー**: `Authorization: Bearer <token>`
- **取得**: `/api/auth/login` エンドポイント

```bash
# 将来の使用例
curl http://localhost:3000/api/todos \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
```

---

## 📡 共通仕様

### リクエストヘッダー

| Header | 必須 | 説明 |
|--------|------|------|
| `Content-Type` | ✅ (POST/PATCH) | `application/json` |
| `Accept` | ❌ | `application/json` (デフォルト) |

### レスポンスヘッダー

| Header | 説明 |
|--------|------|
| `Content-Type` | `application/json; charset=utf-8` |
| `X-Request-Id` | リクエストID（トレース用、将来実装） |

### HTTPステータスコード

| コード | 意味 | 使用例 |
|-------|------|--------|
| `200 OK` | 成功（取得・更新） | `GET /api/todos` |
| `201 Created` | 作成成功 | `POST /api/todos` |
| `204 No Content` | 削除成功 | `DELETE /api/todos/1` |
| `400 Bad Request` | バリデーションエラー | 不正なリクエストボディ |
| `404 Not Found` | リソースが存在しない | 存在しないTodo IDを指定 |
| `500 Internal Server Error` | サーバーエラー | DB接続失敗など |

---

## 📦 共通データ型

### Todo オブジェクト

```typescript
interface Todo {
  id:  number;           // 一意のID（自動採番）
  title: string;        // タイトル（1〜255文字）
  completed: boolean;   // 完了状態
  created_at: string;   // 作成日時（ISO 8601形式）
  updated_at: string;   // 更新日時（ISO 8601形式）
}
```

**例**:
```json
{
  "id": 1,
  "title": "Buy groceries",
  "completed": false,
  "created_at": "2026-01-18T10:30:00.000Z",
  "updated_at": "2026-01-18T10:30:00.000Z"
}
```

---

## 🔄 ページネーション（将来実装）

Phase 2で実装予定：

### クエリパラメータ

| パラメータ | 型 | デフォルト | 説明 |
|-----------|---|-----------|------|
| `page` | integer | 1 | ページ番号 |
| `limit` | integer | 20 | 1ページあたりの件数（最大100） |

### レスポンス形式

```json
{
  "data": [/* Todoオブジェクトの配列 */],
  "meta": {
    "total": 150,
    "page": 1,
    "limit": 20,
    "total_pages": 8
  }
}
```

---

## ❌ エラーレスポンス

詳細は [errors.md](./errors.md) を参照。

### 基本形式

```json
{
  "error": "Validation failed",
  "details": [
    {
      "field": "title",
      "message": "Title is required"
    }
  ]
}
```

---

## 📚 エンドポイント一覧

| エンドポイント | メソッド | 説明 | 詳細 |
|--------------|---------|------|------|
| `/api/todos` | GET | Todo一覧取得 | [todos.md#get-todos](./todos.md#get-todos) |
| `/api/todos` | POST | Todo作成 | [todos.md#create-todo](./todos.md#create-todo) |
| `/api/todos/: id` | PATCH | Todo更新 | [todos.md#update-todo](./todos.md#update-todo) |
| `/api/todos/:id` | DELETE | Todo削除 | [todos.md#delete-todo](./todos.md#delete-todo) |

---

## 🛠️ 開発ツール

### Postman Collection

```bash
# Postman用コレクションをインポート
# 準備中
```

### cURLサンプル集

各エンドポイントのドキュメントにcURLサンプルを記載しています。

### OpenAPI仕様書

機械可読な仕様書:   [openapi.yaml](./openapi.yaml)

Swagger UIで表示（将来実装）: 
```bash
npm run docs: api
# http://localhost:8080 で開く
```

---

## 📖 関連ドキュメント

- [アーキテクチャ設計](../architecture. md)
- [エラーレスポンス仕様](./errors.md)
- [Todosリソース](./todos.md)

---

## 📝 変更履歴

| バージョン | 日付 | 変更内容 |
|-----------|------|---------|
| v1.0.0 | 2026-01-18 | 初版リリース（CRUD操作のみ） |

---

## 💬 フィードバック

API仕様への質問や改善提案は、[GitHub Issues](https://github.com/rising3/todolist-app/issues) までお願いします。
