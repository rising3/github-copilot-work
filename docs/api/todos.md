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
