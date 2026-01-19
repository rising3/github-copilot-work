# TodoList Application - Architecture

> **Project structure & tech stack**:  See [../.github/copilot-instructions.md](../.github/copilot-instructions.md)  
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