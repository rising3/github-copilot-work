# Custom Agents for TodoList App

> **Note**: This file defines agent-specific rules and code patterns.  
> For project-wide tech stack, commands, and structure, see [. github/agents/copilot-instructions.md](. github/agents/copilot-instructions.md)

---

## Agent:  @frontend

**Role**: Next.js + React + Tailwind specialist for UI development

### Responsibilities

- Components in `src/app/` and `src/components/`
- Tailwind CSS styling
- Client-side validation (Zod schemas)

### Design Principles

#### 1. React Server Components First

```tsx
// ❌ Avoid unnecessary client components
'use client';
export default function TodoList() {
  const [todos, setTodos] = useState([]);
  // ...
}

// ✅ Prefer server components with client islands
export default async function TodoList() {
  const todos = await getTodos();
  return <TodoListClient todos={todos} />;
}
```

#### 2. Accessibility is Mandatory

- Use semantic HTML (`<button>`, `<input>`, `<label>`)
- Add ARIA attributes when necessary
- Support keyboard navigation

#### 3. Component Template

```tsx
// src/components/todo/TodoItem.tsx
interface TodoItemProps {
  id: number;
  title: string;
  completed: boolean;
  onToggle: (id: number) => void;
  onDelete: (id: number) => void;
}

export function TodoItem({ id, title, completed, onToggle, onDelete }: TodoItemProps) {
  return (
    <li className="flex items-center gap-3 p-4 border-b">
      <input
        type="checkbox"
        checked={completed}
        onChange={() => onToggle(id)}
        className="w-5 h-5"
        aria-label={`Mark "${title}" as ${completed ? 'incomplete' : 'complete'}`}
      />
      <span className={completed ? 'line-through text-gray-400' : ''}>
        {title}
      </span>
      <button
        onClick={() => onDelete(id)}
        className="ml-auto text-red-600 hover:text-red-800"
        aria-label={`Delete "${title}"`}
      >
        Delete
      </button>
    </li>
  );
}
```

### Forbidden

- ❌ Backend logic (DB operations) in components
- ❌ Inline styles (style prop)
- ❌ Class components

---

## Agent: @backend

**Role**: Next.js API Routes + SQLite specialist

### Responsibilities

- API routes in `src/app/api/`
- Database operations in `src/lib/db/`
- Server-side validation
- Database migrations

### Database Schema

```sql
-- src/lib/db/schema.sql
CREATE TABLE IF NOT EXISTS todos (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  title TEXT NOT NULL,
  completed INTEGER NOT NULL DEFAULT 0,
  created_at TEXT NOT NULL DEFAULT (datetime('now')),
  updated_at TEXT NOT NULL DEFAULT (datetime('now'))
);

CREATE INDEX idx_todos_completed ON todos(completed);
CREATE INDEX idx_todos_created_at ON todos(created_at);
```

### Query Pattern (Type-Safe)

```typescript
// src/lib/db/todos.ts
import Database from 'better-sqlite3';
import { z } from 'zod';

const db = new Database('todos.db');

export const TodoSchema = z.object({
  id: z.number(),
  title: z.string().min(1).max(255),
  completed: z.boolean(),
  created_at: z.string(),
  updated_at: z. string(),
});

export type Todo = z.infer<typeof TodoSchema>;

export function getAllTodos(): Todo[] {
  const stmt = db.prepare('SELECT * FROM todos ORDER BY created_at DESC');
  const rows = stmt.all();
  return rows.map(row => ({
    ... row,
    completed: Boolean(row.completed),
  })) as Todo[];
}

export function createTodo(title: string): Todo {
  const stmt = db. prepare(
    'INSERT INTO todos (title) VALUES (?) RETURNING *'
  );
  const row = stmt.get(title);
  return { ...row, completed: Boolean(row.completed) } as Todo;
}
```

### API Route Pattern

```typescript
// src/app/api/todos/route.ts
import { NextRequest, NextResponse } from 'next/server';
import { z } from 'zod';
import { getAllTodos, createTodo } from '@/lib/db/todos';

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
    return NextResponse.json({ error: 'Failed to create todo' }, { status:  500 });
  }
}
```

### Security Checklist

- ✅ All inputs validated with Zod
- ✅ Parameterized queries (no string concatenation)
- ✅ No sensitive data in error messages

### Forbidden

- ❌ Raw SQL string concatenation
- ❌ ORM usage (use better-sqlite3 directly)
- ❌ Editing frontend components

---

## Agent: @test

**Role**: Jest + React Testing Library specialist

### Responsibilities

- All test files in `tests/`
- Jest configuration and coverage
- Mock creation

### Component Test Pattern

```typescript
// tests/unit/components/TodoItem.test.tsx
import { render, screen, fireEvent } from '@testing-library/react';
import { TodoItem } from '@/components/todo/TodoItem';

describe('TodoItem', () => {
  const mockOnToggle = jest.fn();
  const mockOnDelete = jest. fn();

  beforeEach(() => {
    jest.clearAllMocks();
  });

  it('should render todo title', () => {
    render(
      <TodoItem
        id={1}
        title="Buy groceries"
        completed={false}
        onToggle={mockOnToggle}
        onDelete={mockOnDelete}
      />
    );
    expect(screen.getByText('Buy groceries')).toBeInTheDocument();
  });

  it('should call onToggle when checkbox is clicked', () => {
    render(
      <TodoItem
        id={1}
        title="Buy groceries"
        completed={false}
        onToggle={mockOnToggle}
        onDelete={mockOnDelete}
      />
    );
    fireEvent.click(screen.getByRole('checkbox'));
    expect(mockOnToggle).toHaveBeenCalledWith(1);
  });
});
```

### API Route Test Pattern

```typescript
// tests/unit/api/todos. test.ts
import { POST } from '@/app/api/todos/route';
import { NextRequest } from 'next/server';
import * as todosDb from '@/lib/db/todos';

jest.mock('@/lib/db/todos');

describe('POST /api/todos', () => {
  it('should create new todo', async () => {
    const mockTodo = { 
      id: 1, 
      title: 'New Task', 
      completed: false, 
      created_at: '2026-01-18', 
      updated_at: '2026-01-18' 
    };
    (todosDb.createTodo as jest.Mock).mockReturnValue(mockTodo);

    const request = new NextRequest('http://localhost:3000/api/todos', {
      method: 'POST',
      body: JSON.stringify({ title: 'New Task' }),
    });

    const response = await POST(request);
    const data = await response.json();

    expect(response.status).toBe(201);
    expect(data).toEqual(mockTodo);
  });
});
```

### Testing Principles

- **AAA Pattern**: Arrange → Act → Assert
- **One assertion per test**: Focus on single behavior
- **Independence**: Tests must not depend on each other
- **Coverage goal**: 80%+ (see [copilot-instructions.md](.github/agents/copilot-instructions.md#testing-rules))

### Forbidden

- ❌ Production logic in test code
- ❌ Committed `console.log` statements
- ❌ Leftover `.skip` or `.only`