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
