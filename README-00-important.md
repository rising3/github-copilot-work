# github-copilot Core

## 📋結論(推奨構成)

| ファイル | 唯一の責務 | 参照元 |
| --- | --- | --- |
| copilot-instructions.md | 技術スタック・コマンド・最近の変更（自動生成）| すべてのファイルから参照される
| AGENTS.md | エージェント固有のルールとサンプルコード | copilot-instructions.mdを参照 |
| docs/introduction.md | プロジェクトの目的・特徴・学習目標	| copilot-instructions.mdを参照 |
| docs/architecture.md | 設計思想・技術選定理由・データフロー図 | copilot-instructions. mdとintroduction.mdを参照 |

## 📂 推奨ディレクトリ構成

```
プロジェクト構造: 
├── .github/
│   ├── copilot-instructions.md              # 全体ルール
│   ├── instructions/
│   │   └── react-component.instructions.md  # applyTo:  "src/**/*.tsx"
│   └── agents/
│       └── frontend-specialist.md           # エージェント固有のルールとサンプルコード
├── docs/
│   ├── README.md                            # ドキュメント全体の索引
│   ├── introduction.md                      # プロジェクト概要
│   ├── architecture.md                      # 現在のアーキテクチャ（Living Document）
│   ├── decisions/                           # ADRディレクトリ
│   │   ├── README.md                        # ADR一覧とテンプレート
│   │   └── template.md                      # 新規ADR作成用テンプレート
│   ├── api/
│   │   ├── README.md                        # API全体の概要・認証・共通仕様
│   │   ├── todos.md                         # Todosリソースのエンドポイント
│   │   ├── errors.md                        # エラーレスポンス仕様
│   │   └── openapi.yaml                     # OpenAPI仕様書（オプション）
│   └── guides/                              # 運用ガイド
│       ├── setup.md
│       └── deployment.md
├── AGENTS.md
└── src/
    ├── AGENTS.md
    └── components/
        ├── AGENTS.md
        └── todo/
            └── TodoItem.tsx
```

## メンテナンスフロー

```markdown
技術スタック変更時
└→ . github/copilot-instructions.md のみ更新
   └→ 他のファイルは自動的に参照

エージェントルール追加時
└→ AGENTS.md のみ更新

ビジネス要件変更時
└→ docs/introduction.md のみ更新

技術選定の見直し時
└→ docs/architecture.md のみ更新
```

## 1️⃣ .github/copilot-instructions.md（マスター情報源）

````markdown
# TodoList App Development Guidelines

Auto-generated from all feature plans.  Last updated: 2026-01-18

## Active Technologies

- Node.js 20 LTS + TypeScript 5.3+
- Next.js 15 (App Router)
- Jest 29+ + React Testing Library
- SQLite 3 (better-sqlite3)
- Tailwind CSS 3.4+
- Zod (validation)

## Project Structure

```text
src/
  app/              # Next.js App Router pages & API routes
    api/todos/      # REST API endpoints
  components/       # React components
    ui/             # Reusable UI components (Button, Input, etc.)
    todo/           # Todo-specific components
  lib/              # Utilities, DB client, validation
    db/             # SQLite schema & queries
  hooks/            # Custom React hooks
  types/            # TypeScript type definitions
tests/
  unit/             # Jest unit tests
  integration/      # Integration tests
docs/               # Project documentation
public/             # Static assets
```

## Commands

| Command | Description |
|---------|-------------|
| `npm run dev` | Start dev server (http://localhost:3000) |
| `npm run build` | Production build |
| `npm run start` | Start production server |
| `npm test` | Run all tests |
| `npm run test:watch` | Run tests in watch mode |
| `npm run test:coverage` | Generate coverage report |
| `npm run lint` | Run ESLint |
| `npm run type-check` | Run TypeScript compiler check |
| `npm run db: migrate` | Run database migrations |
| `npm run db:seed` | Seed database with sample data |

## Code Style

- **TypeScript**:  Strict mode enabled, prefer explicit types
- **React**:  Functional components with hooks, no class components
- **Imports**: Use absolute imports with `@/` prefix
- **Naming**: camelCase for variables/functions, PascalCase for components
- **File names**: kebab-case for files, PascalCase for React components

## Database Rules

- **ORM禁止**: Direct SQL queries with better-sqlite3
- **Migration required**: Never modify schema without migration
- **Transactions**: Use transactions for multi-step operations
- **Type safety**: All DB queries must have TypeScript interfaces

## Testing Rules

- **Coverage target**: 80% minimum
- **Test file naming**: `*.test.ts` or `*.test.tsx`
- **Mock external dependencies**: Always mock DB and external APIs
- **Arrange-Act-Assert pattern**: Follow AAA pattern consistently

## Recent Changes

- 2026-01-18: Initial setup with Node.js 20 LTS + Next.js 15 + Jest + SQLite
- 2026-01-18: Added TypeScript strict mode configuration
- 2026-01-18: Configured Tailwind CSS with custom theme

<!-- MANUAL ADDITIONS START -->

## Security Rules

- **Input validation**: All user inputs must be validated with Zod
- **SQL injection prevention**: Use parameterized queries only
- **XSS prevention**: Sanitize all user-generated content
- **CORS**:  Restrict API endpoints to same-origin by default

## Performance Guidelines

- **Server Components**: Default to React Server Components
- **Client Components**: Only use `'use client'` when necessary
- **Database**: Create indexes for frequently queried columns
- **Images**: Use Next.js Image component for optimization

<!-- MANUAL ADDITIONS END -->
```
````

## 2️⃣ AGENTS.md（エージェント固有の実装パターンのみ）

**改善ポイント:**

- 技術スタックやディレクトリ構成は削除
- copilot-instructions.mdへの参照を明記
- 具体的なコードサンプルとルールに特化

````markdown
# TodoList Application - Introduction

> **Technical details** (stack, commands, structure): See [../.github/copilot-instructions.md](../.github/copilot-instructions.md)

## 🎯 Project Purpose

A **simple, fast task management web application** designed as a: 
- Learning resource for modern web development
- Reference implementation for production-ready code
- Demonstration of GitHub Copilot custom agents

## 🚀 Key Features

- ✅ Create, edit, and delete todos
- ✅ Toggle completion status
- ✅ Filter by status (all/active/completed)
- ✅ Responsive design
- ✅ Dark mode support (planned)

## 🎓 Learning Objectives

By working with this project, you will master: 

1. **Next.js App Router** practical usage patterns
2. **Direct database access** with SQLite (no ORM)
3. **Robust validation** with Zod
4. **Test-driven development** with Jest + React Testing Library
5. **GitHub Copilot custom agents** for team productivity

## 🎯 Target Audience

- Frontend developers learning React/Next.js
- Full-stack developers exploring lightweight backend architectures
- Teams wanting to leverage GitHub Copilot effectively

## 📊 Success Metrics

- **Performance**: First Contentful Paint < 1.5s
- **Quality**: 80%+ test coverage
- **Accessibility**: WCAG 2.1 AA compliance
- **Developer Experience**: < 5min setup time

## 🗺️ Project Roadmap

### Phase 1: MVP (Current)
- [x] Basic CRUD operations
- [x] Local SQLite storage
- [ ] Responsive UI

### Phase 2: Enhancement
- [ ] User authentication (NextAuth.js)
- [ ] Dark mode
- [ ] Internationalization (i18n)

### Phase 3: Scale
- [ ] Migrate to PostgreSQL
- [ ] Real-time sync (WebSocket)
- [ ] PWA support

## 📚 Related Documentation

- [Architecture Design](./architecture.md) - System design and technical decisions
- [Development Guidelines](../.github/agents/copilot-instructions.md) - Tech stack and commands
- [Custom Agents](../AGENTS.md) - Role-specific coding patterns
````

## 3️⃣ docs/introduction.md（ビジネス文脈のみ）

**改善ポイント:**

- 技術スタックの詳細は削除（copilot-instructions.mdへ参照）
- プロジェクトの目的・学習目標・ビジョンに特化

````markdown
# TodoList Application - Introduction

> **Technical details** (stack, commands, structure): See [.. /. github/agents/copilot-instructions.md](../.github/agents/copilot-instructions.md)

## 🎯 Project Purpose

A **simple, fast task management web application** designed as a: 
- Learning resource for modern web development
- Reference implementation for production-ready code
- Demonstration of GitHub Copilot custom agents

## 🚀 Key Features

- ✅ Create, edit, and delete todos
- ✅ Toggle completion status
- ✅ Filter by status (all/active/completed)
- ✅ Responsive design
- ✅ Dark mode support (planned)

## 🎓 Learning Objectives

By working with this project, you will master: 

1. **Next.js App Router** practical usage patterns
2. **Direct database access** with SQLite (no ORM)
3. **Robust validation** with Zod
4. **Test-driven development** with Jest + React Testing Library
5. **GitHub Copilot custom agents** for team productivity

## 🎯 Target Audience

- Frontend developers learning React/Next.js
- Full-stack developers exploring lightweight backend architectures
- Teams wanting to leverage GitHub Copilot effectively

## 📊 Success Metrics

- **Performance**: First Contentful Paint < 1.5s
- **Quality**: 80%+ test coverage
- **Accessibility**: WCAG 2.1 AA compliance
- **Developer Experience**: < 5min setup time

## 🗺️ Project Roadmap

### Phase 1: MVP (Current)
- [x] Basic CRUD operations
- [x] Local SQLite storage
- [ ] Responsive UI

### Phase 2: Enhancement
- [ ] User authentication (NextAuth.js)
- [ ] Dark mode
- [ ] Internationalization (i18n)

### Phase 3: Scale
- [ ] Migrate to PostgreSQL
- [ ] Real-time sync (WebSocket)
- [ ] PWA support

## 📚 Related Documentation

- [Architecture Design](./architecture.md) - System design and technical decisions
- [Development Guidelines](../.github/agents/copilot-instructions.md) - Tech stack and commands
- [Custom Agents](../AGENTS.md) - Role-specific coding patterns
````

## 4️⃣ docs/architecture.md（設計判断のみ）

**改善ポイント:**

- ディレクトリ構成は削除（copilot-instructions.mdへ参照）
- 技術選定の理由・トレードオフ・設計判断に特化

````markdown
# TodoList Application - Architecture

> **Project structure & tech stack**:  See [../.github/agents/copilot-instructions.md](../.github/agents/copilot-instructions.md)  
> **Project purpose**:  See [./introduction.md](./introduction.md)

## 🏗️ System Architecture Diagram

```
┌─────────────────────────────────────────────────────────┐
│                   Browser (Client)                      │
│  ┌───────────────────────────────────────────────────┐  │
│  │  React Server Components + Client Islands         │  │
│  └───────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────┘
                           ↕ HTTP
┌─────────────────────────────────────────────────────────┐
│              Next.js Server (Node.js 20 LTS)            │
│  ┌───────────────────────────────────────────────────┐  │
│  │         API Routes (REST) + Zod Validation        │  │
│  └───────────────────────────────────────────────────┘  │
│                           ↕                             │
│  ┌───────────────────────────────────────────────────┐  │
│  │     Database Layer (better-sqlite3 + TypeScript)  │  │
│  └───────────────────────────────────────────────────┘  │
│                           ↕                             │
│  ┌───────────────────────────────────────────────────┐  │
│  │           SQLite Database (todos.db)              │  │
│  └───────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────┘
```

## 🎯 Technical Decisions & Rationale

### Why Next.js 15 App Router? 

**Chosen for**:
- React Server Components reduce JavaScript sent to client
- Unified codebase for frontend + backend (API Routes)
- File-based routing for intuitive development

**Trade-offs**:
- Steep learning curve (Server/Client component mental model)
- Longer build times compared to Vite
- More complex than traditional SPA

**Alternatives considered**:
- Remix:  Better nested routing, but smaller ecosystem
- Vite + Express: More control, but requires separate deployments

---

### Why SQLite (without ORM)?

**Chosen for**:
- Zero-config setup (no database server needed)
- Fast read/write for single-user apps
- File-based portability (easy backup/restore)
- Learning opportunity for raw SQL

**Trade-offs**: 
- Poor concurrent write performance (not for multi-user production)
- Requires migration to PostgreSQL/MySQL for production scale
- More boilerplate code than Prisma/Drizzle

**Why no ORM**: 
```typescript
// With ORM (Prisma) - Hidden complexity
const todos = await prisma.todo.findMany({
  where: { completed: true },
  orderBy: { createdAt:  'desc' }
});

// Without ORM - Explicit SQL
const stmt = db.prepare('SELECT * FROM todos WHERE completed = 1 ORDER BY created_at DESC');
const todos = stmt.all();
```
- **Transparency**: See exactly what query runs
- **Performance**: No N+1 query surprises
- **Learning**: Reinforces SQL fundamentals

---

### Why Jest over Vitest?

**Chosen for**:
- Mature ecosystem (10+ years)
- React Testing Library official support
- More Stack Overflow answers

**Trade-offs**:
- Slower than Vitest (no ESM support)
- Requires Babel/SWC transformation

**When to switch**:  If build times exceed 30s

---

### Why Zod over Yup/Joi?

**Chosen for**: 
- TypeScript-first (infers types from schema)
- Runtime validation + static types in one
- Composable schemas

```typescript
// Schema definition = TypeScript type
export const TodoSchema = z.object({
  title: z.string().min(1).max(255),
  completed: z.boolean(),
});

export type Todo = z.infer<typeof TodoSchema>; // Automatic type inference
```

---

## 🔄 Data Flow Patterns

### Read Pattern (Server Component)

```
User → Page. tsx (RSC) → DB Query → SQLite
          ↓
       HTML (pre-rendered) → Browser
```

### Write Pattern (Client Component)

```
User → Form → POST /api/todos → Zod Validation
                                      ↓
                                 INSERT Query
                                      ↓
                                   SQLite
                                      ↓
              Revalidate Path ← Success Response
                     ↓
                Re-render RSC
```

## 🔒 Security Architecture

### Defense in Depth

```
┌─────────────────────────────────────────┐
│ Layer 1: Client-Side Validation (UX)    │ ← HTML5 + Zod
├─────────────────────────────────────────┤
│ Layer 2: Server-Side Validation         │ ← Zod (REQUIRED)
├─────────────────────────────────────────┤
│ Layer 3: Parameterized Queries          │ ← better-sqlite3
├─────────────────────────────────────────┤
│ Layer 4: React Auto-Escaping            │ ← XSS prevention
└─────────────────────────────────────────┘
```

**Critical Rules**:
- Client validation is **optional** (can be bypassed)
- Server validation is **mandatory** (never trust client)
- SQL queries must **always** use parameters

---

## 📊 Performance Strategy

### Database Optimization

```sql
-- Index for filtering by completion status
CREATE INDEX idx_todos_completed ON todos(completed);

-- Index for sorting by date
CREATE INDEX idx_todos_created_at ON todos(created_at DESC);
```

**Query Plan**:
```sql
EXPLAIN QUERY PLAN SELECT * FROM todos WHERE completed = 0 ORDER BY created_at DESC;
-- Should use idx_todos_completed and idx_todos_created_at
```

### Frontend Optimization

- **Default**:  Server Components (no JS to client)
- **Interactive parts**: Client Components with `'use client'`
- **Images**: Next.js `<Image>` for automatic WebP conversion
- **Fonts**: `next/font` for self-hosted fonts (no external requests)

---

## 🚀 Deployment Architecture

### Development
```bash
npm run dev  # localhost:3000 + SQLite
```

### Production (Recommended)

**Vercel** (zero-config):
```bash
git push origin main  # Auto-deploys
```

**Docker** (self-hosted):
```dockerfile
FROM node:20-alpine
WORKDIR /app
COPY package*. json ./
RUN npm ci --production
COPY . .
RUN npm run build
CMD ["npm", "start"]
```

**⚠️ Production Migration Path**:
```
Development:      SQLite (better-sqlite3)
                     ↓
Staging:         PostgreSQL (node-postgres)
                     ↓
Production:      PostgreSQL + Prisma (for migrations)
```

---

## 🧪 Testing Strategy

| Level | Tool | Coverage Target | Purpose |
|-------|------|-----------------|---------|
| Unit | Jest | 80%+ | Individual functions |
| Component | React Testing Library | 90%+ | UI behavior |
| Integration | Jest + Supertest | 70%+ | API endpoints |
| E2E | Playwright (future) | Critical paths | User workflows |

**Testing Pyramid**:
```
        /\
       /E2E\        ← Few (slow, expensive)
      /──────\
     /Integ.  \      ← Some (medium speed)
    /──────────\
   /   Unit     \   ← Many (fast, cheap)
  /──────────────\
```

---

## 🔮 Migration Path

### Current State (MVP)
- Single-user app
- Local SQLite
- No authentication

### Phase 2 (Multi-User)
```diff
- SQLite (better-sqlite3)
+ PostgreSQL (Prisma)
+ NextAuth. js (authentication)
+ Row-Level Security (RLS)
```

### Phase 3 (Scale)
```diff
+ Redis (caching)
+ WebSocket (real-time)
+ CDN (static assets)
+ Monitoring (Sentry + Datadog)
```

---

## 📖 Further Reading

- [Why We Don't Use ORMs](https://github.com/rising3/todolist-app/docs/decisions/002-no-orm.md) (ADR)
- [SQLite Performance Tuning](https://www.sqlite.org/optoverview.html)
- [Next.js Server Components RFC](https://github.com/reactjs/rfcs/blob/main/text/0188-server-components.md)
````