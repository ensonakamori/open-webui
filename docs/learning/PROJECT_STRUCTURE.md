# Project Structure

**A complete guide to Open WebUI's file and folder organization.** This document helps you find what you're looking for quickly.

**Estimated reading time:** 30-45 minutes
**Prerequisites:** [GETTING_STARTED.md](./GETTING_STARTED.md)
**Documented:** November 18, 2025

---

## 📋 Table of Contents

1. [Repository Overview](#repository-overview)
2. [Frontend Structure (`src/`)](#frontend-structure-src)
3. [Backend Structure (`backend/`)](#backend-structure-backend)
4. [Configuration Files](#configuration-files)
5. [Testing](#testing)
6. [Deployment](#deployment)
7. [Finding What You Need](#finding-what-you-need)
8. [Naming Conventions](#naming-conventions)
9. [Import Patterns](#import-patterns)
10. [Next Steps](#next-steps)

---

## Repository Overview

### Top-Level Structure

```
open-webui/
├── src/                     # Frontend (Svelte + TypeScript)
├── backend/                 # Backend (Python + FastAPI)
├── static/                  # Static assets (images, etc.)
├── docs/                    # Documentation
│   └── learning/           # 📚 Learning materials (you are here!)
├── cypress/                # E2E tests
├── test/                   # Additional tests
├── kubernetes/             # K8s deployment manifests
├── scripts/                # Build/utility scripts
├── #claude/                # Claude AI context (ignore)
├── package.json            # Frontend dependencies
├── pyproject.toml          # Backend dependencies
├── vite.config.ts          # Vite configuration
├── svelte.config.js        # SvelteKit configuration
├── tailwind.config.js      # Tailwind CSS configuration
├── tsconfig.json           # TypeScript configuration
└── README.md               # Main project README
```

### 🧠 Mental Model: Two Main Directories

```
┌──────────────┐          ┌──────────────┐
│     src/     │          │   backend/   │
│              │          │              │
│   Frontend   │  ────>   │   Backend    │
│   (Svelte)   │   HTTP   │  (FastAPI)   │
│              │          │              │
└──────────────┘          └──────────────┘
     ↓ Build                    ↓ Package
  dist/ or                  Python package
  build/                    (pip install)
```

🌉 **Next.js Developers:** Similar to `app/` and `pages/api/` but completely separate

---

## Frontend Structure (`src/`)

### Overview

**Location:** `/src/`
**Purpose:** SvelteKit application (UI, routing, client logic)
**Language:** TypeScript + Svelte

```
src/
├── lib/                    # Shared library code
│   ├── apis/              # API client functions
│   ├── components/        # Reusable Svelte components
│   ├── i18n/              # Internationalization (i18next)
│   ├── pyodide/           # Pyodide (Python in browser)
│   ├── stores/            # Svelte stores (global state)
│   ├── types/             # TypeScript type definitions
│   ├── utils/             # Utility functions
│   └── workers/           # Web Workers
├── routes/                # SvelteKit file-based routing
│   ├── (app)/            # Main application routes
│   ├── auth/             # Authentication pages
│   ├── error/            # Error pages
│   ├── s/                # Shared/public routes
│   └── watch/            # Watch/observe routes
├── app.css               # Global styles
├── app.d.ts              # TypeScript ambient declarations
└── app.html              # HTML template
```

---

### `src/lib/` - Shared Library

**Purpose:** Code shared across the application

#### `src/lib/apis/` - API Client Layer

**Purpose:** Functions to call backend API endpoints

**Structure:**
```
src/lib/apis/
├── index.ts              # Main API exports & common functions
├── auths/                # Authentication API
├── chats/                # Chat management API
├── channels/             # Channels/collaboration API
├── configs/              # Configuration API
├── evaluations/          # Evaluations API
├── files/                # File upload/download API
├── folders/              # Folder organization API
├── functions/            # Custom functions API
├── groups/               # User groups API
├── images/               # Image generation API
├── knowledge/            # Knowledge bases/RAG API
├── memories/             # Memory management API
├── models/               # Model configuration API
├── notes/                # Notes API
├── ollama/               # Ollama integration API
...and more
```

**Example:** [src/lib/apis/chats/index.ts](../../src/lib/apis/chats/)

**Pattern:**
```typescript
// Each API module exports functions
export const getChats = async (token: string) => {
  const res = await fetch('/api/v1/chats/', {
    headers: { Authorization: `Bearer ${token}` }
  });
  return res.json();
};
```

🎯 **Remember:** One folder per API domain

---

#### `src/lib/components/` - Svelte Components

**Purpose:** Reusable UI components organized by feature

**Structure:**
```
src/lib/components/
├── AddConnectionModal.svelte     # Modals (top-level)
├── AddToolServerModal.svelte
├── ChangelogModal.svelte
├── ImportModal.svelte
├── admin/                         # Admin panel components
│   ├── Settings/
│   ├── Users/
│   ├── Models/
│   └── ...
├── app/                           # App-level components
│   ├── Navbar.svelte
│   └── ...
├── channel/                       # Channel/collaboration
│   ├── ChannelList.svelte
│   └── ...
├── chat/                          # Chat interface (largest folder)
│   ├── Chat.svelte               # Main chat component
│   ├── Messages/
│   ├── MessageInput/
│   ├── Settings/
│   └── ...
├── common/                        # Shared/generic components
│   ├── Button.svelte
│   ├── Modal.svelte
│   ├── Spinner.svelte
│   └── ...
├── icons/                         # Icon components (huge!)
│   ├── ChevronDown.svelte
│   ├── Send.svelte
│   └── ...hundreds more
├── layout/                        # Layout components
│   ├── Sidebar.svelte
│   ├── Navbar.svelte
│   ├── Overlay/
│   └── ...
├── notes/                         # Notes feature components
├── playground/                    # LLM playground components
└── workspace/                     # Workspace management
    ├── Models/
    ├── Knowledge/
    ├── Prompts/
    ├── Tools/
    └── ...
```

**✅ Pattern:** Feature-based organization

**🎯 Remember:**
- Top-level = Shared/modal components
- Subdirectories = Feature-specific components

---

#### `src/lib/stores/` - Global State

**Purpose:** Svelte stores for application-wide state

**Structure:**
```
src/lib/stores/
└── index.ts              # All stores exported here
```

**Key stores:**
```typescript
// User authentication
export const user = writable(null);

// Application settings
export const settings = writable({});
export const config = writable({});

// LLM-related
export const models = writable([]);
export const prompts = writable([]);
export const tools = writable([]);
export const functions = writable([]);

// UI state
export const showSettings = writable(false);
export const showSidebar = writable(true);
export const showShortcuts = writable(false);

// Data
export const chats = writable([]);
export const tags = writable([]);
export const knowledge = writable([]);
```

**Usage:**
```svelte
<script lang="ts">
  import { user, settings } from '$lib/stores';

  // Access store value
  console.log($user);

  // Update store
  $user = { ...newUserData };
</script>
```

🌉 **React Developers:** Similar to Redux/Zustand stores, but simpler

**📍 See:** [FRONTEND_ARCHITECTURE.md](./FRONTEND_ARCHITECTURE.md) for store patterns

---

#### `src/lib/types/` - TypeScript Types

**Purpose:** Shared TypeScript type definitions

**Structure:**
```
src/lib/types/
└── (TypeScript .d.ts or .ts files with types)
```

**Example:**
```typescript
export interface Chat {
  id: string;
  title: string;
  created_at: number;
  updated_at: number;
  // ...
}

export interface Message {
  id: string;
  role: 'user' | 'assistant';
  content: string;
  // ...
}
```

---

#### `src/lib/utils/` - Utility Functions

**Purpose:** Helper functions, formatters, validators

**Structure:**
```
src/lib/utils/
├── index.ts              # Main utilities
└── (various .ts files with utilities)
```

**Examples:**
- Date/time formatting
- String manipulation
- Validation helpers
- Local storage helpers
- Markdown processing

---

#### `src/lib/i18n/` - Internationalization

**Purpose:** Translation files and i18next configuration

**Structure:**
```
src/lib/i18n/
├── locales/              # Translation JSON files
│   ├── en-US/           # English (US)
│   ├── fr-FR/           # French
│   ├── es-ES/           # Spanish
│   └── ...
└── (i18n configuration)
```

**Adding a new language:**
1. Copy `en-US/` folder
2. Translate JSON values
3. Add to `languages.json`

**📍 See:** [CONTRIBUTING.md](../../docs/CONTRIBUTING.md#-translations-and-internationalization)

---

#### `src/lib/pyodide/` - Python in Browser

**Purpose:** Pyodide integration for client-side Python execution

**Structure:**
```
src/lib/pyodide/
└── (Pyodide setup and utilities)
```

**Use cases:**
- Code interpreter in chat
- Data analysis
- Custom Python functions

**✅ CURRENT:** Pyodide 0.28 with Python 3.13

---

#### `src/lib/workers/` - Web Workers

**Purpose:** Background processing off the main thread

**Structure:**
```
src/lib/workers/
└── (Web Worker implementations)
```

**Use cases:**
- Heavy computations
- Large file processing
- Background tasks

---

### `src/routes/` - SvelteKit Routing

**Purpose:** File-based routing (pages/endpoints)

**🧠 Mental Model:** Files = Routes

```
File                              →  Route
src/routes/+page.svelte           →  /
src/routes/auth/+page.svelte      →  /auth
src/routes/(app)/home/+page.svelte → /home
src/routes/(app)/c/[id]/+page.svelte → /c/123
```

#### SvelteKit File Naming Convention

| File | Purpose |
|------|---------|
| `+page.svelte` | Page component (renders UI) |
| `+page.ts` | Page load function (data fetching) |
| `+page.server.ts` | Server-side page load (SSR only) |
| `+layout.svelte` | Layout wrapper for child routes |
| `+layout.ts` | Layout load function |
| `+server.ts` | API endpoint |
| `+error.svelte` | Error page for route |

🌉 **Next.js Developers:** Similar to `page.tsx`, `layout.tsx`, `route.ts`

---

#### `src/routes/(app)/` - Main Application

**Purpose:** Main authenticated application routes
**Note:** Parentheses `(app)` create a route group (doesn't affect URL)

**Structure:**
```
src/routes/(app)/
├── +layout.svelte         # Main app layout (sidebar, navbar)
├── +page.svelte           # Home/dashboard
├── admin/                 # Admin panel
│   ├── settings/         # Admin settings
│   ├── users/            # User management
│   ├── models/           # Model configuration
│   └── ...
├── c/                     # Chat routes
│   └── [id]/             # Dynamic chat ID
│       └── +page.svelte  # Chat page
├── channels/              # Collaboration channels
│   └── [id]/
├── home/                  # Home page
├── notes/                 # Notes feature
│   ├── +page.svelte      # Notes list
│   └── [id]/             # Individual note
├── playground/            # LLM playground
└── workspace/             # Workspace routes
    ├── models/           # Model management
    ├── knowledge/        # Knowledge bases
    ├── prompts/          # Prompt library
    ├── tools/            # Tools management
    ├── functions/        # Functions management
    └── ...
```

**Access control:** Requires authentication (enforced in `+layout.svelte`)

---

#### `src/routes/auth/` - Authentication

**Purpose:** Login/signup pages

**Structure:**
```
src/routes/auth/
└── +page.svelte          # Login/signup form
```

**Route:** `/auth`

---

#### `src/routes/s/` - Share/Public Routes

**Purpose:** Public shared content (no auth required)

**Structure:**
```
src/routes/s/
└── [id]/                 # Shared item by ID
    └── +page.svelte
```

**Example:** `/s/abc123` - View shared chat/document

---

### Frontend Build Output

**Development:**
- Vite dev server serves files directly
- No build output (in-memory)

**Production:**
```
build/                    # SvelteKit build output
├── client/              # Client-side assets
│   ├── _app/           # Application code (hashed)
│   └── ...
└── server/              # Server-side code (if using adapter-node)
```

---

## Backend Structure (`backend/`)

### Overview

**Location:** `/backend/`
**Purpose:** FastAPI application (API, database, LLM logic)
**Language:** Python

```
backend/
├── open_webui/           # Main Python package
│   ├── routers/         # API route handlers
│   ├── models/          # Database models (SQLAlchemy)
│   ├── socket/          # WebSocket handlers
│   ├── migrations/      # Database migrations (Alembic)
│   ├── retrieval/       # RAG implementation
│   ├── storage/         # Storage backends (S3, local, etc.)
│   ├── utils/           # Utility modules
│   ├── internal/        # Internal modules
│   ├── config.py        # Configuration
│   ├── constants.py     # Constants
│   ├── main.py          # FastAPI app entry point
│   └── __init__.py
├── data/                # Data directory (gitignored)
└── (test files)
```

---

### `backend/open_webui/routers/` - API Endpoints

**Purpose:** FastAPI routers organized by domain

**Structure:**
```
routers/
├── audio.py              # Audio/TTS/STT endpoints
├── auths.py              # Authentication endpoints
├── channels.py           # Channel/collaboration endpoints
├── chats.py              # Chat management endpoints
├── configs.py            # Configuration endpoints
├── evaluations.py        # Model evaluation endpoints
├── files.py              # File upload/download endpoints
├── folders.py            # Folder organization endpoints
├── functions.py          # Custom Python functions endpoints
├── groups.py             # User group endpoints
├── images.py             # Image generation endpoints
├── knowledge.py          # Knowledge base/RAG endpoints
├── memories.py           # Memory management endpoints
├── models.py             # Model configuration endpoints
├── notes.py              # Notes endpoints
├── ollama.py             # Ollama integration endpoints
├── openai.py             # OpenAI/compatible API endpoints
├── pipelines.py          # Custom pipeline endpoints
├── prompts.py            # Prompt template endpoints
├── retrieval.py          # RAG/vector search endpoints (large!)
├── scim.py               # SCIM provisioning endpoints
├── tasks.py              # Background task endpoints
├── tools.py              # Custom tools endpoints
├── users.py              # User management endpoints
└── utils.py              # Utility endpoints
```

**Pattern:** Each router = `/api/v1/{router_name}/*`

**Example:** [audio.py](../../backend/open_webui/routers/audio.py)
```python
from fastapi import APIRouter

router = APIRouter()

@router.post("/audio/speech")
async def generate_speech(...):
    # Text-to-speech implementation
    pass
```

**Mounted in:** [main.py](../../backend/open_webui/main.py#L70-L96)

🎯 **Remember:** One router file per API domain

---

### `backend/open_webui/models/` - Database Models

**Purpose:** SQLAlchemy ORM models (database schema)

**Structure:**
```
models/
├── auths.py              # Auth tokens, sessions
├── chats.py              # Chats and messages
├── channels.py           # Collaboration channels
├── configs.py            # System configuration
├── evaluations.py        # Model evaluations
├── files.py              # File metadata
├── folders.py            # Folder organization
├── functions.py          # Custom functions
├── groups.py             # User groups
├── knowledge.py          # Knowledge bases
├── memories.py           # Conversation memory
├── models.py             # LLM model configurations
├── notes.py              # Notes
├── prompts.py            # Prompt templates
├── tools.py              # Custom tools
├── users.py              # User accounts
└── ...
```

**✅ CURRENT:** SQLAlchemy 2.0 syntax

**Example:**
```python
from sqlalchemy import Column, String, BigInteger, Text
from open_webui.internal.db import Base

class Chat(Base):
    __tablename__ = "chat"

    id = Column(String, primary_key=True)
    user_id = Column(String)
    title = Column(Text)
    created_at = Column(BigInteger)
    # ...
```

**📍 See:** [DATABASE_ARCHITECTURE.md](./DATABASE_ARCHITECTURE.md)

---

### `backend/open_webui/migrations/` - Database Migrations

**Purpose:** Alembic migrations (schema changes)

**Structure:**
```
migrations/
├── env.py                # Alembic environment
├── versions/             # Migration files
│   ├── 001_initial.py
│   ├── 002_add_channels.py
│   └── ...
└── alembic.ini
```

**Create migration:**
```bash
# From project root
alembic -c backend/open_webui/alembic.ini revision -m "Add new table"
```

**Apply migrations:**
```bash
# From project root
alembic -c backend/open_webui/alembic.ini upgrade head
```

**📍 See:** [DATABASE_ARCHITECTURE.md](./DATABASE_ARCHITECTURE.md#migrations)

---

### `backend/open_webui/socket/` - WebSocket Handlers

**Purpose:** Real-time communication (Socket.io)

**Structure:**
```
socket/
├── main.py               # Socket.io app & event handlers
└── ...
```

**Use cases:**
- Live chat updates
- Typing indicators
- User presence
- Channel synchronization

**Example:**
```python
@sio.on('message')
async def handle_message(sid, data):
    # Broadcast message to channel
    await sio.emit('message', data, room=channel_id)
```

---

### `backend/open_webui/retrieval/` - RAG Implementation

**Purpose:** Vector embeddings and retrieval

**Structure:**
```
retrieval/
├── (vector database integrations)
├── (embedding functions)
└── (retrieval logic)
```

**Supported vector DBs:**
- ChromaDB (default)
- Milvus
- Qdrant
- Pinecone
- Postgres w/ pgvector

**📍 See:** [INTEGRATION_GUIDE.md](./INTEGRATION_GUIDE.md#rag-retrieval-augmented-generation)

---

### `backend/open_webui/storage/` - Storage Backends

**Purpose:** File storage abstraction

**Supported:**
- Local filesystem
- AWS S3
- Azure Blob Storage
- Google Cloud Storage

---

### `backend/open_webui/utils/` - Utility Modules

**Purpose:** Helper functions and utilities

**Structure:**
```
utils/
├── audit.py              # Audit logging
├── logger.py             # Logging configuration
├── (various utilities)
└── ...
```

---

### Backend Package Structure

**As a Python package:**
```bash
pip install -e .          # Installs open-webui package
```

**Entry point:** `backend/open_webui/main.py`

**Import pattern:**
```python
from open_webui.models.users import Users
from open_webui.utils import logger
from open_webui.routers import chats
```

---

## Configuration Files

### `package.json` - Frontend Dependencies

**Location:** `/package.json`

**Key sections:**
```json
{
  "scripts": {
    "dev": "vite dev",
    "build": "vite build",
    "lint": "...",
    "test:frontend": "vitest"
  },
  "dependencies": {
    "svelte": "^5.0.0",
    "@sveltejs/kit": "^2.5.27",
    // ...
  },
  "devDependencies": {
    "vite": "^5.4.14",
    "typescript": "^5.5.4",
    // ...
  }
}
```

---

### `pyproject.toml` - Backend Dependencies

**Location:** `/pyproject.toml`

**Key sections:**
```toml
[project]
name = "open-webui"
dependencies = [
    "fastapi==0.118.0",
    "sqlalchemy==2.0.38",
    "langchain==0.3.27",
    # ...
]

[project.optional-dependencies]
postgres = [
    "psycopg2-binary==2.9.10",
    "pgvector==0.4.1",
]
```

**Install with extras:**
```bash
pip install -e ".[postgres]"
```

---

### `vite.config.ts` - Vite Configuration

**Location:** `/vite.config.ts`

**Key settings:**
- Proxy configuration (frontend → backend)
- Build options
- Plugins
- Aliases

**Example:**
```typescript
export default defineConfig({
  server: {
    proxy: {
      '/api': 'http://localhost:8080',  // Proxy API calls
    }
  }
});
```

---

### `svelte.config.js` - SvelteKit Configuration

**Location:** `/svelte.config.js`

**Key settings:**
- Adapter (static, node, etc.)
- Preprocessing
- Aliases

---

### `tailwind.config.js` - Tailwind CSS

**Location:** `/tailwind.config.js`

**✅ CURRENT:** Tailwind CSS v4 (NEW config approach!)

**⚠️ Note:** Tailwind v4 uses CSS-first configuration, so this file is minimal

**📍 See:** [TECH_STACK_GUIDE.md](./TECH_STACK_GUIDE.md#tailwind-css-v4)

---

### `tsconfig.json` - TypeScript Configuration

**Location:** `/tsconfig.json`

**Key settings:**
- Compiler options
- Path aliases (`$lib/*`, etc.)
- Type checking strictness

---

## Testing

### Frontend Tests

**Location:** Throughout `src/` as `.test.ts` or `.spec.ts`
**Runner:** Vitest

**Run:**
```bash
npm run test:frontend
```

### E2E Tests

**Location:** `/cypress/`
**Runner:** Cypress

**Run:**
```bash
npm run cy:open
```

### Backend Tests

**Location:** `/backend/open_webui/test/`
**Runner:** Pytest

**🔍 NEEDS VERIFICATION:** Test organization and coverage

---

## Deployment

### Docker

**Files:**
- `Dockerfile` - Main production image
- `docker-compose.yaml` - Docker Compose setup
- `docker-compose.*.yaml` - Various deployment scenarios

### Kubernetes

**Location:** `/kubernetes/`

**Structure:**
```
kubernetes/
├── manifest/             # Raw Kubernetes manifests
│   ├── base/            # Base configuration
│   └── (overlays)       # Kustomize overlays
└── helm/                # Helm chart
    ├── Chart.yaml
    ├── values.yaml
    └── templates/
```

**Deploy with kubectl:**
```bash
kubectl apply -k ./kubernetes/manifest
```

**Deploy with Helm:**
```bash
helm install open-webui ./kubernetes/helm/
```

---

## Finding What You Need

### Quick Reference

| I want to... | Look in... |
|--------------|-----------|
| Add a new page | `src/routes/` |
| Add a UI component | `src/lib/components/` |
| Call an API | `src/lib/apis/` |
| Add global state | `src/lib/stores/` |
| Add an API endpoint | `backend/open_webui/routers/` |
| Change database schema | `backend/open_webui/models/` |
| Configure the app | `backend/open_webui/config.py` |
| Add a translation | `src/lib/i18n/locales/` |
| Add tests | Same directory as code |

### Search Tips

**Finding components:**
```bash
# Find all components with "Chat" in name
find src/lib/components -name "*Chat*"

# Find component usage
grep -r "import.*ChatComponent" src/
```

**Finding API calls:**
```bash
# Find where an API is called
grep -r "getChats" src/lib/

# Find API endpoint definition
grep -r "@router.get.*chats" backend/
```

---

## Naming Conventions

### Frontend (TypeScript/Svelte)

**Files:**
- Components: `PascalCase.svelte` (e.g., `ChatMessage.svelte`)
- Utilities: `camelCase.ts` (e.g., `formatDate.ts`)
- Types: `PascalCase.ts` or `.d.ts`
- Routes: `+page.svelte`, `+layout.svelte`

**Variables:**
- camelCase for variables and functions
- UPPER_SNAKE_CASE for constants
- PascalCase for types/interfaces

**Example:**
```typescript
const userName = 'John';         // variable
const MAX_LENGTH = 100;          // constant
interface UserData { ... }       // type
function getUserName() { ... }   // function
```

### Backend (Python)

**Files:**
- snake_case.py (e.g., `user_management.py`)
- Models: singular (e.g., `user.py`, `chat.py`)
- Routers: plural (e.g., `users.py`, `chats.py`)

**Variables:**
- snake_case for everything
- UPPER_SNAKE_CASE for constants
- PascalCase for classes

**Example:**
```python
user_name = 'John'               # variable
MAX_LENGTH = 100                 # constant
class UserModel:                 # class
    pass

def get_user_name():             # function
    pass
```

---

## Import Patterns

### Frontend (Svelte/TypeScript)

**Aliases:**
```typescript
import { Component } from '$lib/components/Component.svelte';
import { user } from '$lib/stores';
import { getChats } from '$lib/apis/chats';
import type { Chat } from '$lib/types';
import { goto } from '$app/navigation';
import { page } from '$app/stores';
```

**Defined in:** `svelte.config.js` and `tsconfig.json`

### Backend (Python)

**Absolute imports:**
```python
from open_webui.models.users import Users
from open_webui.utils.logger import logger
from open_webui.routers.chats import router
```

**Relative imports (within same package):**
```python
from .models import Users
from ..utils import logger
```

🎯 **Prefer:** Absolute imports for clarity

---

## Next Steps

Now that you know where everything is:

1. 🧠 **Understand the tech:** [TECH_STACK_GUIDE.md](./TECH_STACK_GUIDE.md)
   - Deep dive into each technology

2. 🌊 **Follow the data:** [DATA_FLOW_GUIDE.md](./DATA_FLOW_GUIDE.md)
   - Trace requests end-to-end

3. 🎨 **Frontend deep dive:** [FRONTEND_ARCHITECTURE.md](./FRONTEND_ARCHITECTURE.md)
   - Svelte 5, SvelteKit patterns

4. ⚙️ **Backend deep dive:** [BACKEND_ARCHITECTURE.md](./BACKEND_ARCHITECTURE.md)
   - FastAPI, SQLAlchemy patterns

5. 🛠️ **Start building:** [HOW_TO_GUIDE.md](./HOW_TO_GUIDE.md)
   - Add features step-by-step

---

## Summary

**Key takeaways:**

✅ **Two main directories:** `src/` (frontend) and `backend/` (backend)

✅ **Feature-based organization:** Components and APIs grouped by domain

✅ **File-based routing:** Files in `src/routes/` become URLs

✅ **Clear separation:** Each layer has distinct responsibilities

✅ **Configuration:** Multiple config files for different tools

✅ **Naming conventions:** Consistent patterns across the codebase

**Ready to dive deeper?** → [TECH_STACK_GUIDE.md](./TECH_STACK_GUIDE.md)

---

**Last updated:** November 18, 2025
**Tech stack research:** [TECH_STACK_RESEARCH.md](./TECH_STACK_RESEARCH.md)
