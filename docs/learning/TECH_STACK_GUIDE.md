# Technology Stack Guide

**A comprehensive guide to every technology used in Open WebUI.** Learn what each tool does, why it was chosen, and how to use it effectively.

**Estimated reading time:** 2-3 hours
**Prerequisites:** [GETTING_STARTED.md](./GETTING_STARTED.md), [ARCHITECTURE_OVERVIEW.md](./ARCHITECTURE_OVERVIEW.md)
**Documented:** November 18, 2025

---

## 📋 Table of Contents

1. [Overview](#overview)
2. [Frontend Technologies](#frontend-technologies)
3. [Backend Technologies](#backend-technologies)
4. [Database & Storage](#database--storage)
5. [AI/ML Technologies](#aiml-technologies)
6. [Development Tools](#development-tools)
7. [Technology Comparison Matrix](#technology-comparison-matrix)
8. [Next Steps](#next-steps)

---

## Overview

### Technology Stack at a Glance

```
┌─────────────────────────────────────────┐
│           FRONTEND STACK                │
├─────────────────────────────────────────┤
│ Svelte 5         - Component framework  │
│ SvelteKit 2      - Meta-framework       │
│ TypeScript 5.5   - Type safety          │
│ Tailwind CSS v4  - Styling              │
│ Vite 5           - Build tool           │
└─────────────────────────────────────────┘

┌─────────────────────────────────────────┐
│           BACKEND STACK                 │
├─────────────────────────────────────────┤
│ Python 3.11-3.12 - Language             │
│ FastAPI 0.118    - Web framework        │
│ SQLAlchemy 2.0   - ORM                  │
│ Pydantic v2      - Validation           │
│ Uvicorn          - ASGI server          │
└─────────────────────────────────────────┘

┌─────────────────────────────────────────┐
│           DATA & AI STACK               │
├─────────────────────────────────────────┤
│ SQLite/PostgreSQL - Database            │
│ Redis            - Cache & sessions     │
│ ChromaDB 1.0     - Vector database      │
│ LangChain 0.3    - LLM framework        │
│ Transformers     - ML models            │
└─────────────────────────────────────────┘
```

---

## Frontend Technologies

### 1. Svelte 5

**What it is:** JavaScript framework for building user interfaces
**Version:** 5.0.0 (⚠️ Project is 43 patches behind latest 5.43.8)
**Status:** ✅ **CURRENT** - Using latest paradigm (Runes)

#### 🧠 Mental Model

Think of Svelte as a **compiler** rather than a runtime framework:

```
Write Code → Svelte Compiler → Highly Optimized Vanilla JS
```

Unlike React/Vue which ship a runtime library, Svelte compiles your components to efficient imperative code that surgically updates the DOM.

#### 🌉 For React Developers

| Concept | React | Svelte 5 |
|---------|-------|----------|
| **State** | `useState()` | `$state()` |
| **Derived** | `useMemo()` | `$derived()` |
| **Effects** | `useEffect()` | `$effect()` |
| **Props** | `props.value` | `$props()` |
| **Context** | `useContext()` | `getContext()` |

**Key Difference:** Svelte's reactivity is built-in, not bolted on through hooks.

#### ✅ CURRENT Pattern: Runes (Svelte 5)

```svelte
<script lang="ts">
  // ✅ Svelte 5 Runes (CURRENT)
  let count = $state(0);
  let doubled = $derived(count * 2);

  $effect(() => {
    console.log('Count is:', count);
  });

  function increment() {
    count += 1;  // Direct mutation!
  }
</script>

<button onclick={increment}>
  {count} (doubled: {doubled})
</button>
```

#### 🚨 OUTDATED Pattern: Svelte 3/4

```svelte
<script>
  // ❌ OLD - Don't use this pattern!
  let count = 0;
  $: doubled = count * 2;
  $: console.log('Count is:', count);
</script>
```

#### 💡 Aha Moment

**Runes are explicit, hooks are implicit:**
- React: `useState` creates reactive state via closures
- Svelte: `$state` explicitly marks reactive variables

#### 🎯 Remember This

**"Dollar signs mark magic"** - Any `$` prefix means Svelte compiler magic:
- `$state` - reactive variable
- `$derived` - computed value
- `$effect` - side effect
- `$props` - component props

#### ⚠️ Common Pitfall

**Destructuring props loses reactivity:**
```svelte
<script lang="ts">
  // ❌ BAD - Loses reactivity
  let { count } = $props();

  // ✅ GOOD - Keeps reactivity
  let props = $props();
  // Use: props.count
</script>
```

#### Official Resources

- **Docs:** https://svelte.dev/docs/svelte/overview
- **Tutorial:** https://svelte.dev/tutorial/svelte/welcome-to-svelte
- **Blog:** https://svelte.dev/blog/whats-new-in-svelte-november-2025
- **Migration:** https://svelte.dev/docs/svelte/v5-migration-guide

---

### 2. SvelteKit 2

**What it is:** Meta-framework for Svelte (routing, SSR, build)
**Version:** 2.5.27 (🚨 **Significantly behind** - latest is 2.48.5)
**Status:** ⚠️ Missing latest features like Remote Functions

#### 🧠 Mental Model

SvelteKit is to Svelte what Next.js is to React:

```
Svelte      : React
SvelteKit   : Next.js
```

It adds:
- File-based routing
- Server-side rendering (SSR)
- API endpoints
- Build optimizations

#### 🌉 For Next.js Developers

| Feature | Next.js | SvelteKit |
|---------|---------|-----------|
| **Pages** | `page.tsx` | `+page.svelte` |
| **Layouts** | `layout.tsx` | `+layout.svelte` |
| **API Routes** | `route.ts` | `+server.ts` |
| **Data Fetching** | `async function` in component | `load` in `+page.ts` |
| **Routing** | `/app` or `/pages` | `/src/routes` |

#### File-Based Routing

```
src/routes/
├── +page.svelte              → /
├── about/+page.svelte        → /about
├── blog/
│   ├── +page.svelte          → /blog
│   └── [slug]/
│       └── +page.svelte      → /blog/:slug
└── (app)/                    ← Route group (no URL segment)
    └── dashboard/+page.svelte → /dashboard
```

#### Special Files

| File | Purpose |
|------|---------|
| `+page.svelte` | Page component |
| `+page.ts` | Load data for page |
| `+page.server.ts` | Server-only data loading |
| `+layout.svelte` | Shared layout |
| `+layout.ts` | Shared data loading |
| `+server.ts` | API endpoint |
| `+error.svelte` | Error boundary |

#### Loading Data

```typescript
// +page.ts
export async function load({ fetch, params }) {
  const res = await fetch(`/api/posts/${params.slug}`);
  const post = await res.json();

  return { post };
}
```

```svelte
<!-- +page.svelte -->
<script lang="ts">
  let { data } = $props();  // Access loaded data
</script>

<h1>{data.post.title}</h1>
```

#### 💡 Aha Moment

**The `+` prefix is intentional** - it makes route files easy to spot among component files.

#### ⚠️ Common Pitfall

**Universal vs Server-only load:**
- `+page.ts` - Runs on **both** server and client
- `+page.server.ts` - Runs **only** on server (can use secrets)

#### Official Resources

- **Docs:** https://svelte.dev/docs/kit/introduction
- **Routing:** https://svelte.dev/docs/kit/routing
- **Loading Data:** https://svelte.dev/docs/kit/load

---

### 3. TypeScript 5.5

**What it is:** JavaScript with static typing
**Version:** 5.5.4 (⚠️ **4 minor versions behind** - latest is 5.9)
**Status:** ✅ Modern, but missing 5.8's 2-3x faster builds

#### Why TypeScript?

**Type safety catches bugs before runtime:**
```typescript
// ❌ JavaScript - Runtime error
function greet(name) {
  return name.toUpperCase();
}
greet(123);  // Runtime error: toUpperCase is not a function

// ✅ TypeScript - Compile-time error
function greet(name: string) {
  return name.toUpperCase();
}
greet(123);  // Error: Argument of type 'number' not assignable to 'string'
```

#### TypeScript in Svelte

```svelte
<script lang="ts">
  interface User {
    id: string;
    name: string;
    email: string;
  }

  let users = $state<User[]>([]);

  async function fetchUsers(): Promise<User[]> {
    const res = await fetch('/api/users');
    return res.json();
  }
</script>
```

#### 🎯 Remember This

**TypeScript doesn't run** - It's erased at build time. It's purely for development.

#### Official Resources

- **Handbook:** https://www.typescriptlang.org/docs/handbook/intro.html
- **Docs:** https://www.typescriptlang.org/docs/

---

### 4. Tailwind CSS v4

**What it is:** Utility-first CSS framework
**Version:** 4.0.0 (✅ **CURRENT** - Latest major version!)
**Status:** 🆕 **NEW IN 2025** - Released January 22, 2025

#### 🧠 Mental Model

Instead of writing CSS classes, you compose utility classes:

```html
<!-- ❌ Traditional CSS -->
<button class="primary-button">Click me</button>

<style>
.primary-button {
  background-color: blue;
  color: white;
  padding: 0.5rem 1rem;
  border-radius: 0.25rem;
}
</style>

<!-- ✅ Tailwind -->
<button class="bg-blue-500 text-white px-4 py-2 rounded">
  Click me
</button>
```

#### 🆕 What's New in Tailwind v4?

**CSS-First Configuration (MAJOR CHANGE!):**

```css
/* ✅ Tailwind v4 - Configure in CSS */
@import "tailwindcss";

@theme {
  --color-primary: #3b82f6;
  --font-sans: 'Inter', sans-serif;
}
```

```javascript
// ❌ Tailwind v3 - Required tailwind.config.js
module.exports = {
  theme: {
    extend: {
      colors: {
        primary: '#3b82f6'
      }
    }
  }
}
```

#### Key Improvements

- ⚡ **5x faster** full builds
- ⚡ **100x+ faster** incremental builds
- 🔍 **Automatic content detection** - No need to configure file paths
- 🎨 **Native CSS variables** - Design tokens as CSS custom properties
- 📦 **Container queries** - Built-in (no plugin)
- 🎭 **3D transforms** - `rotate-x-*`, `rotate-y-*`, `scale-z-*`
- 🌈 **Advanced gradients** - Linear, radial, conic

#### ⚠️ IMPORTANT

**Most Tailwind tutorials online show v3 patterns** - They're outdated!

Look for "Tailwind v4" or "Tailwind CSS 2025" specifically.

#### Official Resources

- **Docs:** https://tailwindcss.com/docs
- **V4 Announcement:** https://tailwindcss.com/blog/tailwindcss-v4

---

### 5. Vite 5

**What it is:** Build tool and dev server
**Version:** 5.4.14 (🚨 **One major version behind** - Vite 6 released Nov 2024)
**Status:** ⚠️ Missing Environment API and latest features

#### 🧠 Mental Model

Vite = **Lightning-fast dev server** + **Optimized production bundler**

**Development:**
```
Source code → ESM (native modules) → Browser
             ↑ Instant (no bundling!)
```

**Production:**
```
Source code → Rollup bundler → Optimized chunks
```

#### Why Vite?

**Compared to Webpack:**
- ⚡ **Dev server:** Instant (vs 10-30s)
- ⚡ **HMR:** <50ms (vs 1-5s)
- 📦 **Modern:** ESM-first, optimized for modern browsers

#### 🌉 For Create React App Users

| Feature | CRA (Webpack) | Vite |
|---------|---------------|------|
| **Dev start** | 10-30 seconds | < 1 second |
| **HMR** | 1-5 seconds | <50ms |
| **Build** | 30-60 seconds | 10-20 seconds |
| **Config** | Complex | Simple |

#### Vite Config

```typescript
// vite.config.ts
export default defineConfig({
  plugins: [sveltekit()],
  server: {
    proxy: {
      '/api': 'http://localhost:8080'  // Proxy API calls to backend
    }
  }
});
```

#### 💡 Aha Moment

**Vite doesn't bundle in development** - It serves raw ES modules. That's why it's so fast!

#### Official Resources

- **Docs:** https://vite.dev/
- **Guide:** https://vite.dev/guide/

---

### 6. Additional Frontend Tools

#### TipTap 3.0

**What it is:** Headless rich text editor
**Version:** 3.0.7 (⚠️ **10 minor versions behind** - latest is 3.10.7)
**Status:** ✅ Using latest major version (v3 stable since July 2025)

**Usage in Open WebUI:** Chat input, notes editor

**🌉 Comparison:** Like Draft.js or Slate, but framework-agnostic

#### CodeMirror 6

**What it is:** Code editor component
**Usage:** Code blocks in chat, Python function editor

#### Pyodide 0.28

**What it is:** Python running in the browser via WebAssembly
**Status:** ✅ Uses Python 3.13

**Use cases:**
- Code interpreter in chat
- Client-side data processing
- Custom Python functions

#### Chart.js 4.5

**What it is:** Chart/graph library
**Usage:** Visualizations in chat

---

## Backend Technologies

### 1. Python 3.11-3.12

**What it is:** Programming language
**Version Required:** 3.11 - 3.12
**Latest Available:** 3.14 (just released November 2025)
**Status:** ✅ Well-supported (security updates until 2027-2028)

#### Why Python?

- 🤖 **AI/ML ecosystem** - Best ML libraries (PyTorch, TensorFlow, Transformers)
- 📚 **LLM tools** - LangChain, LlamaIndex, ChromaDB all Python-first
- ⚡ **Async/await** - Modern async capabilities
- 🔧 **Type hints** - Static typing with mypy

#### Modern Python Patterns

```python
# Type hints
def greet(name: str) -> str:
    return f"Hello, {name}"

# Async/await
async def fetch_data(url: str) -> dict:
    async with aiohttp.ClientSession() as session:
        async with session.get(url) as resp:
            return await resp.json()

# Pattern matching (Python 3.10+)
match response:
    case {"status": "ok", "data": data}:
        process(data)
    case {"status": "error", "message": msg}:
        handle_error(msg)
```

---

### 2. FastAPI 0.118

**What it is:** Modern async web framework for Python
**Version:** 0.118.0 (⚠️ **3 minor versions behind** - latest is 0.121)
**Status:** ✅ **CURRENT** - No breaking changes, stable

#### 🧠 Mental Model

FastAPI = **Express.js** (simplicity) + **Flask** (Python) + **Type Safety** (Pydantic)

#### 🌉 For Express.js Developers

| Feature | Express.js | FastAPI |
|---------|-----------|---------|
| **Routing** | `app.get('/users')` | `@app.get("/users")` |
| **Params** | `req.params.id` | `id: str` (function param) |
| **Body** | `req.body` | Pydantic model |
| **Validation** | Manual (Joi, etc.) | Automatic (Pydantic) |
| **Async** | Promises/async | `async def` |
| **Docs** | Manual (Swagger) | Auto-generated |

#### Basic FastAPI Example

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel

app = FastAPI()

class User(BaseModel):
    name: str
    email: str
    age: int

@app.get("/users/{user_id}")
async def get_user(user_id: str) -> User:
    # Type hints = automatic validation + docs!
    user = await db.get_user(user_id)
    if not user:
        raise HTTPException(status_code=404, detail="User not found")
    return user

@app.post("/users")
async def create_user(user: User) -> User:
    # Request body automatically validated against User model
    return await db.create_user(user)
```

#### 💡 Aha Moment

**FastAPI generates API docs automatically** from your type hints!

Visit `/docs` for Swagger UI, `/redoc` for ReDoc.

#### Dependency Injection

```python
from fastapi import Depends

async def get_current_user(token: str) -> User:
    # Verify token, return user
    return user

@app.get("/me")
async def read_current_user(current_user: User = Depends(get_current_user)):
    # current_user is automatically injected!
    return current_user
```

#### 🎯 Remember This

**"Type hints are not just documentation"** - FastAPI uses them for:
- Request validation
- Response serialization
- API documentation
- OpenAPI schema generation

#### Official Resources

- **Docs:** https://fastapi.tiangolo.com/
- **Tutorial:** https://fastapi.tiangolo.com/tutorial/

---

### 3. SQLAlchemy 2.0

**What it is:** Python ORM (Object-Relational Mapping)
**Version:** 2.0.38 (⚠️ **6 patches behind** - latest is 2.0.44)
**Status:** ✅ **CURRENT** - Using latest paradigm (2.0 syntax)

#### 🚨 CRITICAL: SQLAlchemy 2.0 ≠ 1.x

SQLAlchemy 2.0 is **completely different** from 1.x. Old tutorials are **invalid**.

#### 🌉 For TypeORM/Prisma Developers

| Feature | TypeORM/Prisma | SQLAlchemy 2.0 |
|---------|----------------|----------------|
| **Define Model** | Classes with decorators | Python classes |
| **Query** | `.find()`, `.findOne()` | `select()` statement |
| **Relations** | Automatic joins | Explicit `joinedload()` |
| **Migrations** | Auto-generate | Alembic |
| **Async** | Native | `AsyncSession` |

#### ✅ CURRENT: SQLAlchemy 2.0 Pattern

```python
from sqlalchemy import select
from sqlalchemy.ext.asyncio import AsyncSession

# Define model
class User(Base):
    __tablename__ = "users"

    id: Mapped[str] = mapped_column(String, primary_key=True)
    name: Mapped[str] = mapped_column(String)
    email: Mapped[str] = mapped_column(String, unique=True)

# Query (NEW 2.0 syntax)
async def get_user(session: AsyncSession, user_id: str) -> User | None:
    stmt = select(User).where(User.id == user_id)
    result = await session.execute(stmt)
    return result.scalar_one_or_none()

# Create
async def create_user(session: AsyncSession, user: User) -> User:
    session.add(user)
    await session.commit()
    await session.refresh(user)
    return user
```

#### 🚨 OUTDATED: SQLAlchemy 1.x Pattern

```python
# ❌ DON'T USE - This is 1.x syntax
user = session.query(User).filter(User.id == user_id).first()
```

#### 💡 Aha Moment

**`select()` creates a statement, not a query** - You build up the statement, then execute it:

```python
stmt = select(User)          # Build statement
stmt = stmt.where(...)       # Add conditions
stmt = stmt.order_by(...)    # Add ordering
result = await session.execute(stmt)  # Execute
```

#### Official Resources

- **Docs:** https://docs.sqlalchemy.org/en/20/
- **2.0 Migration:** https://docs.sqlalchemy.org/en/20/changelog/migration_20.html

---

### 4. Pydantic v2

**What it is:** Data validation using Python type hints
**Version:** 2.x (✅ **CURRENT** - v1 is deprecated)
**Status:** ✅ Required for FastAPI

#### What Pydantic Does

```python
from pydantic import BaseModel, EmailStr, Field

class User(BaseModel):
    name: str = Field(min_length=1, max_length=100)
    email: EmailStr  # Automatically validates email format
    age: int = Field(ge=0, le=150)  # Greater/equal 0, less/equal 150

# ✅ Valid
user = User(name="John", email="john@example.com", age=30)

# ❌ Raises ValidationError
user = User(name="", email="invalid", age=-5)
```

#### 🎯 Remember This

**Pydantic v1 is deprecated** - FastAPI now requires v2. Check your dependencies!

---

### 5. Uvicorn

**What it is:** ASGI server (runs FastAPI apps)
**What it does:** Like `node` for Express, or `gunicorn` for Flask

**Development:**
```bash
uvicorn open_webui.main:app --reload
```

**Production:**
```bash
uvicorn open_webui.main:app --workers 4
```

---

## Database & Storage

### 1. SQLite / PostgreSQL

**SQLite** - Default (file-based, simple)
**PostgreSQL** - Production (scalable, features)

**When to use each:**
- **SQLite:** Development, small deployments, single server
- **PostgreSQL:** Production, high traffic, multiple servers

### 2. Redis

**What it is:** In-memory key-value store
**Usage in Open WebUI:**
- Session storage
- Caching API responses
- Rate limiting
- WebSocket pub/sub

### 3. ChromaDB 1.0

**What it is:** Vector database for embeddings
**Version:** 1.0.20 (🚨 **3 minor versions behind** - latest is 1.3.3)
**Status:** ✅ Top 5% popular vector database

**Use in RAG:**
```
Document → Split → Embed → Store in ChromaDB
                                  ↓
User Query → Embed → Search ChromaDB → Relevant chunks
```

**Alternatives supported:**
- Milvus
- Qdrant
- Pinecone
- pgvector (PostgreSQL extension)

---

## AI/ML Technologies

### 1. LangChain

**What it is:** Framework for building LLM applications
**Version:** 0.3.27 (🚨 **DEPRECATED** - v1.0.7 available)
**Status:** ⚠️ **CRITICAL** - Project uses deprecated API

#### What LangChain Does

**Without LangChain:**
```python
# Different API for each provider
openai_response = openai.ChatCompletion.create(...)
claude_response = anthropic.Completion.create(...)
```

**With LangChain:**
```python
# Unified interface
from langchain.chat_models import ChatOpenAI, ChatAnthropic

model = ChatOpenAI()  # or ChatAnthropic()
response = model.predict("Hello!")
```

#### 🚨 Migration Needed: v0.3 → v1.0

**Breaking changes:**
- `create_react_agent` deprecated → use `create_agent`
- Python 3.9 dropped (requires 3.10+)
- Some APIs moved to `langchain-classic`

**Recommendation:** Plan migration to v1.0

---

### 2. HuggingFace Transformers

**What it is:** ML model library (state-of-the-art models)
**Usage:**
- Embedding generation
- Model fine-tuning
- Custom ML tasks

---

### 3. Sentence Transformers

**What it is:** Library for text embeddings
**Usage:** Generate embeddings for RAG

```python
from sentence_transformers import SentenceTransformer

model = SentenceTransformer('all-MiniLM-L6-v2')
embeddings = model.encode(["Hello world", "Goodbye"])
```

---

## Development Tools

### 1. ESLint + Prettier

**ESLint:** JavaScript/TypeScript linter
**Prettier:** Code formatter

### 2. Black + Pylint

**Black:** Python code formatter (opinionated)
**Pylint:** Python linter

### 3. Vitest

**What it is:** Unit test framework for Vite projects
**Usage:** Frontend testing

### 4. Cypress

**What it is:** E2E testing framework
**Usage:** Full user flow testing

### 5. Alembic

**What it is:** Database migration tool for SQLAlchemy
**Usage:** Schema versioning

---

## Technology Comparison Matrix

### Frontend Framework Comparison

| Framework | Bundle Size | Performance | Learning Curve | Ecosystem |
|-----------|------------|-------------|----------------|-----------|
| **Svelte 5** | 15kb | Excellent | Gentle | Growing |
| React 19 | 45kb | Good | Moderate | Massive |
| Vue 3 | 34kb | Good | Gentle | Large |
| Angular | 100kb+ | Good | Steep | Large |

**Winner for this project:** Svelte (smaller size, better perf)

### Backend Framework Comparison

| Framework | Language | Async | Type Safety | Docs |
|-----------|----------|-------|-------------|------|
| **FastAPI** | Python | Native | Excellent | Auto |
| Express | JavaScript | Promises | TypeScript | Manual |
| Django | Python | Limited | Good | Manual |
| NestJS | TypeScript | Native | Excellent | Manual |

**Winner for this project:** FastAPI (async, types, AI ecosystem)

---

## Next Steps

Now that you understand the technologies:

1. 🌊 **See them in action:** [DATA_FLOW_GUIDE.md](./DATA_FLOW_GUIDE.md)
   - Trace requests through the stack

2. 🎨 **Frontend deep dive:** [FRONTEND_ARCHITECTURE.md](./FRONTEND_ARCHITECTURE.md)
   - Svelte 5 patterns, SvelteKit routing

3. ⚙️ **Backend deep dive:** [BACKEND_ARCHITECTURE.md](./BACKEND_ARCHITECTURE.md)
   - FastAPI patterns, SQLAlchemy queries

4. 🛠️ **Start building:** [HOW_TO_GUIDE.md](./HOW_TO_GUIDE.md)
   - Apply what you learned

---

## Summary

**Key takeaways:**

✅ **Svelte 5 Runes** - New paradigm, not like Svelte 3/4
✅ **Tailwind v4** - CSS-first config, brand new
✅ **SQLAlchemy 2.0** - Completely different from 1.x
✅ **LangChain 0.3** - Deprecated, migrate to 1.0
✅ **FastAPI + Pydantic** - Type safety throughout
✅ **Modern async** - Both frontend and backend

**Ready for deeper dives?** → [DATA_FLOW_GUIDE.md](./DATA_FLOW_GUIDE.md)

---

**Last updated:** November 18, 2025
**Tech stack research:** [TECH_STACK_RESEARCH.md](./TECH_STACK_RESEARCH.md)
