# API ドキュメント戦略

## 💡 ベストプラクティス

- 実行可能なサンプル: すべてのcURLコマンドはコピペで動作する
- 型定義を明示: TypeScriptインターフェースを記載
- エラーケースを網羅: 正常系だけでなく異常系も詳細に
- 実装例を提示: Next.jsの実装コードを併記
- 将来の拡張を示唆: Phase 2の計画を記載

## 📋結論(推奨構成)

| ファイル | 内容 | 対象読者 |
| --- | --- | --- |
| api/README.md | API全体の概要・認証・共通仕様 | API初心者・新規開発者 |
| api/todos.md | Todosリソースの全エンドポイント詳細 | フロントエンド開発者 |
| api/errors.md | エラーレスポンス仕様とトラブルシューティング | デバッグ時の参照 |

## 📂 推奨ディレクトリ構成

``` markdown
docs/
├── api/
│   ├── README.md              # API全体の概要・認証・共通仕様
│   ├── todos.md               # Todosリソースのエンドポイント
│   ├── errors.md              # エラーレスポンス仕様
│   └── openapi.yaml           # OpenAPI仕様書（オプション）
```
## 1️⃣ docs/api/README.md（API概要・共通仕様）

```` markdown
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
````

## 2️⃣ docs/api/todos.md（Todosリソースの詳細）

````markdown
# Todos API

Todoリソースの管理エンドポイント。

---

## エンドポイント一覧

- [GET /api/todos](#get-todos) - Todo一覧取得
- [POST /api/todos](#create-todo) - Todo作成
- [PATCH /api/todos/:id](#update-todo) - Todo更新
- [DELETE /api/todos/:id](#delete-todo) - Todo削除

---

## GET /api/todos

すべてのTodoを取得します。

### リクエスト

```http
GET /api/todos HTTP/1.1
Host: localhost:3000
Accept: application/json
```

#### クエリパラメータ

| パラメータ | 型 | 必須 | 説明 | デフォルト |
|-----------|---|------|------|-----------|
| `completed` | boolean | ❌ | 完了状態でフィルタ (`true`/`false`) | なし（全件） |

#### 例:  未完了のTodoのみ取得

```bash
curl "http://localhost:3000/api/todos?completed=false"
```

---

### レスポンス

#### 成功 (200 OK)

```json
[
  {
    "id":  1,
    "title":  "Buy groceries",
    "completed": false,
    "created_at": "2026-01-18T10:30:00.000Z",
    "updated_at": "2026-01-18T10:30:00.000Z"
  },
  {
    "id": 2,
    "title": "Write documentation",
    "completed": true,
    "created_at":  "2026-01-18T11:00:00.000Z",
    "updated_at": "2026-01-18T12:00:00.000Z"
  }
]
```

#### 空の場合

```json
[]
```

#### エラー (500 Internal Server Error)

```json
{
  "error": "Failed to fetch todos"
}
```

---

### 実装例（Next.js）

```typescript
// src/app/api/todos/route.ts
export async function GET(request: NextRequest) {
  try {
    const { searchParams } = new URL(request.url);
    const completed = searchParams.get('completed');
    
    const todos = completed !== null
      ? getTodosByStatus(completed === 'true')
      : getAllTodos();
    
    return NextResponse.json(todos);
  } catch (error) {
    return NextResponse.json(
      { error: 'Failed to fetch todos' },
      { status: 500 }
    );
  }
}
```

---

## POST /api/todos

新しいTodoを作成します。

### リクエスト

```http
POST /api/todos HTTP/1.1
Host: localhost:3000
Content-Type: application/json

{
  "title": "Buy groceries"
}
```

#### リクエストボディ

| フィールド | 型 | 必須 | 制��� | 説明 |
|-----------|---|------|------|------|
| `title` | string | ✅ | 1〜255文字 | Todoのタイトル |

#### cURL サンプル

```bash
curl -X POST http://localhost:3000/api/todos \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Buy groceries"
  }'
```

---

### レスポンス

#### 成功 (201 Created)

```json
{
  "id": 3,
  "title": "Buy groceries",
  "completed":  false,
  "created_at": "2026-01-18T14:30:00.000Z",
  "updated_at": "2026-01-18T14:30:00.000Z"
}
```

#### バリデーションエラー (400 Bad Request)

```json
{
  "error": [
    {
      "code": "too_small",
      "minimum": 1,
      "type": "string",
      "inclusive": true,
      "exact": false,
      "message": "Title is required",
      "path": ["title"]
    }
  ]
}
```

**よくあるバリデーションエラー**: 

| エラー | 原因 | 解決策 |
|-------|------|--------|
| `Title is required` | `title`が空文字 | 1文字以上入力 |
| `Title too long` | `title`が255文字超 | 255文字以内に���める |

---

### 実装例（Next.js）

```typescript
// src/app/api/todos/route. ts
import { z } from 'zod';

const CreateTodoSchema = z.object({
  title: z.string().min(1, 'Title is required').max(255, 'Title too long'),
});

export async function POST(request: NextRequest) {
  try {
    const body = await request.json();
    const { title } = CreateTodoSchema.parse(body);
    const newTodo = createTodo(title);
    return NextResponse.json(newTodo, { status: 201 });
  } catch (error) {
    if (error instanceof z.ZodError) {
      return NextResponse. json({ error: error.errors }, { status: 400 });
    }
    return NextResponse.json(
      { error: 'Failed to create todo' },
      { status: 500 }
    );
  }
}
```

---

## PATCH /api/todos/:id

既存のTodoを更新します。

### リクエスト

```http
PATCH /api/todos/1 HTTP/1.1
Host: localhost:3000
Content-Type: application/json

{
  "completed": true
}
```

#### パスパラメータ

| パラメータ | 型 | 説明 |
|-----------|---|------|
| `id` | integer | TodoのID |

#### リクエストボディ

| フィールド | 型 | 必須 | 制約 | 説明 |
|-----------|---|------|------|------|
| `title` | string | ❌ | 1〜255文字 | 新しいタイトル |
| `completed` | boolean | ❌ | - | 完了状態 |

**Note**: 少なくとも1つのフィールドが必要です。

#### cURL サンプル

```bash
# 完了状態を更新
curl -X PATCH http://localhost:3000/api/todos/1 \
  -H "Content-Type: application/json" \
  -d '{"completed": true}'

# タイトルと完了状態を同時更新
curl -X PATCH http://localhost:3000/api/todos/1 \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Buy groceries and cook dinner",
    "completed": true
  }'
```

---

### レスポンス

#### 成功 (200 OK)

```json
{
  "id": 1,
  "title": "Buy groceries",
  "completed": true,
  "created_at":  "2026-01-18T10:30:00.000Z",
  "updated_at":  "2026-01-18T15:00:00.000Z"
}
```

#### Todo が存在しない (404 Not Found)

```json
{
  "error": "Todo not found"
}
```

#### バリデーションエラー (400 Bad Request)

```json
{
  "error": "At least one field (title or completed) is required"
}
```

---

### 実装例（Next.js）

```typescript
// src/app/api/todos/[id]/route.ts
export async function PATCH(
  request: NextRequest,
  { params }: { params: { id: string } }
) {
  try {
    const id = parseInt(params.id);
    const body = await request.json();
    
    const UpdateTodoSchema = z.object({
      title: z.string().min(1).max(255).optional(),
      completed: z. boolean().optional(),
    }).refine(
      data => data.title !== undefined || data.completed !== undefined,
      { message: 'At least one field is required' }
    );
    
    const updates = UpdateTodoSchema.parse(body);
    const updatedTodo = updateTodo(id, updates);
    
    if (!updatedTodo) {
      return NextResponse.json(
        { error: 'Todo not found' },
        { status:  404 }
      );
    }
    
    return NextResponse.json(updatedTodo);
  } catch (error) {
    // エラーハンドリング
  }
}
```

---

## DELETE /api/todos/:id

Todoを削除します。

### リクエスト

```http
DELETE /api/todos/1 HTTP/1.1
Host: localhost:3000
```

#### パスパラメータ

| パラメータ | 型 | 説明 |
|-----------|---|------|
| `id` | integer | TodoのID |

#### cURL サンプル

```bash
curl -X DELETE http://localhost:3000/api/todos/1
```

---

### レスポンス

#### 成功 (204 No Content)

レスポンスボディなし

#### Todo が存在しない (404 Not Found)

```json
{
  "error": "Todo not found"
}
```

---

### 実装例（Next.js）

```typescript
// src/app/api/todos/[id]/route.ts
export async function DELETE(
  request: NextRequest,
  { params }: { params: { id: string } }
) {
  try {
    const id = parseInt(params.id);
    const deleted = deleteTodo(id);
    
    if (!deleted) {
      return NextResponse.json(
        { error: 'Todo not found' },
        { status: 404 }
      );
    }
    
    return new NextResponse(null, { status: 204 });
  } catch (error) {
    return NextResponse.json(
      { error: 'Failed to delete todo' },
      { status: 500 }
    );
  }
}
```

---

## 🧪 テストケース

### 正常系

```bash
# 1. 一覧取得（空）
curl http://localhost:3000/api/todos
# 期待: []

# 2. Todo作成
curl -X POST http://localhost:3000/api/todos \
  -H "Content-Type: application/json" \
  -d '{"title":  "Test Todo"}'
# 期待: 201 Created

# 3. 一覧取得（1件）
curl http://localhost:3000/api/todos
# 期待: [{"id": 1, "title": "Test Todo", ... }]

# 4. 完了状態を更新
curl -X PATCH http://localhost:3000/api/todos/1 \
  -H "Content-Type: application/json" \
  -d '{"completed": true}'
# 期待: 200 OK

# 5. 削除
curl -X DELETE http://localhost:3000/api/todos/1
# 期待: 204 No Content
```

### 異常系

```bash
# 1. タイトルなしで作成
curl -X POST http://localhost:3000/api/todos \
  -H "Content-Type: application/json" \
  -d '{"title":  ""}'
# 期待:  400 Bad Request

# 2. ��在しないIDを更新
curl -X PATCH http://localhost:3000/api/todos/9999 \
  -H "Content-Type: application/json" \
  -d '{"completed": true}'
# 期待: 404 Not Found

# 3. 存在しないIDを削除
curl -X DELETE http://localhost:3000/api/todos/9999
# 期待: 404 Not Found
```

---

## 📊 パフォーマンス

### レスポンスタイム目標

| エンドポイント | 目標 | 実測（ローカル） |
|--------------|------|----------------|
| GET /api/todos | < 100ms | ~20ms |
| POST /api/todos | < 150ms | ~30ms |
| PATCH /api/todos/:id | < 100ms | ~25ms |
| DELETE /api/todos/:id | < 50ms | ~15ms |

---

## 🔮 将来の拡張

Phase 2で以下を追加予定：

- [ ] `GET /api/todos/:id` - 単一Todo取得
- [ ] `GET /api/todos? sort=created_at&order=desc` - ソート機能
- [ ] `GET /api/todos?page=1&limit=20` - ページネーション
- [ ] `POST /api/todos/bulk` - 一括作成
- [ ] `DELETE /api/todos` - 完了済みTodoの一括削除
````

## 3️⃣ docs/api/errors.md（エラーレスポンス仕様）

````makrdown
# Error Responses

API エラーレスポンスの詳細仕様。

---

## エラーレスポンス形式

### 基本構造

```typescript
interface ErrorResponse {
  error: string | object;  // エラーメッセージまたは詳細オブジェクト
  details?: Array<{        // オプション: バリデーションエラー詳細
    field: string;
    message: string;
    code?:  string;
  }>;
}
```

---

## HTTPステータスコード別エラー

### 400 Bad Request - バリデーションエラー

**原因**: リクエストボディが不正

#### 例1: 必須フィールドが空

```json
{
  "error": [
    {
      "code":  "too_small",
      "minimum": 1,
      "type": "string",
      "inclusive": true,
      "exact": false,
      "message": "Title is required",
      "path": ["title"]
    }
  ]
}
```

#### 例2: フィールドが長すぎる

```json
{
  "error": [
    {
      "code": "too_big",
      "maximum": 255,
      "type": "string",
      "inclusive": true,
      "exact":  false,
      "message": "Title too long",
      "path": ["title"]
    }
  ]
}
```

#### 例3: 型エラー

```json
{
  "error": [
    {
      "code": "invalid_type",
      "expected": "boolean",
      "received": "string",
      "message": "Expected boolean, received string",
      "path": ["completed"]
    }
  ]
}
```

---

### 404 Not Found - リソースが存在しない

**原因**: 指定されたIDのTodoが存在しない

```json
{
  "error":  "Todo not found"
}
```

**発生するエンドポイント**:
- `PATCH /api/todos/: id`
- `DELETE /api/todos/:id`

---

### 500 Internal Server Error - サーバーエラー

**原因**: データベース接続失敗など

```json
{
  "error":  "Failed to fetch todos"
}
```

**Note**: 本番環境では詳細なエラー情報を返さない（セキュリティ上の理由）

---

## Zodバリデーションエラーコード

| コード | 説明 | 例 |
|-------|------|---|
| `too_small` | 最小値/最小長を下回る | `title`が空文字 |
| `too_big` | 最大値/最大長を超える | `title`が255���字超 |
| `invalid_type` | 型が不正 | `completed`に文字列を指定 |
| `invalid_string` | 文字列形式が不正 | メールアドレス形式違反（将来実装） |

---

## エラーハンドリング例

### クライアント側（TypeScript）

```typescript
async function createTodo(title: string) {
  try {
    const response = await fetch('/api/todos', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body:  JSON.stringify({ title }),
    });
    
    if (!response.ok) {
      const error = await response.json();
      
      if (response.status === 400) {
        // バリデーションエラー
        console.error('Validation errors:', error.error);
        // ユーザーにフィードバック表示
      } else if (response.status === 500) {
        // サーバーエラー
        console. error('Server error:', error.error);
        alert('An error occurred.  Please try again later.');
      }
      
      throw new Error(error. error);
    }
    
    return await response.json();
  } catch (error) {
    console.error('Failed to create todo:', error);
    throw error;
  }
}
```

---

## トラブルシューティング

### よくあるエラーと解決策

| エラー | 原因 | 解決策 |
|-------|------|--------|
| `Title is required` | `title`が空 | 1文字以上入力 |
| `Title too long` | `title`が255文字超 | 255文字以内に |
| `Expected boolean, received string` | `completed`に`"true"`を指定 | `true`（真偽値）を使用 |
| `Todo not found` | 存在しないIDを指定 | `GET /api/todos`で有効なIDを確認 |
| `Failed to fetch todos` | DBエラー | サーバーログを確認 |

---

## デバッグ情報（開発環境のみ）

開発環境（`NODE_ENV=development`）では、詳細なスタックトレースを返します：

```json
{
  "error": "Database connection failed",
  "stack":  "Error: Database connection failed\n    at getAllTodos (/app/lib/db/todos.ts:10:15)\n    ..."
}
```

**⚠️ 本番環境では絶対に返さない**（セキュリティリスク）

---

## 関連ドキュメント

- [API概要](./README.md)
- [Todosエンドポイント](./todos.md)
````
