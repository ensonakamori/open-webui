# Frequently Asked Questions

**Common questions about Open WebUI development.**

**Documented:** November 18, 2025

---

## 🎯 General Questions

### What is Open WebUI?

Open WebUI is a self-hosted web interface for large language models (LLMs) like Ollama, OpenAI, and others. It provides a ChatGPT-like interface with features like:
- Multiple LLM provider support
- RAG (Retrieval Augmented Generation) with document uploads
- User authentication and multi-user support
- Chat history and persistence
- Knowledge bases and tools

### Why these specific technologies?

**Frontend (Svelte 5 + SvelteKit):**
- ✅ Smaller bundle sizes than React/Vue
- ✅ Better performance (compiles to vanilla JS)
- ✅ Simpler reactivity model
- ✅ Built-in SSR and routing with SvelteKit

**Backend (FastAPI + Python):**
- ✅ Python ecosystem for AI/ML libraries (LangChain, ChromaDB, transformers)
- ✅ FastAPI's automatic OpenAPI docs and validation
- ✅ Async support for WebSockets and streaming
- ✅ Type safety with Pydantic

See [ARCHITECTURE_OVERVIEW.md](./ARCHITECTURE_OVERVIEW.md) for deeper analysis.

### Is Open WebUI production-ready?

Yes! Open WebUI is actively used in production by thousands of users. However:
- Use PostgreSQL instead of SQLite for production
- Configure proper authentication and rate limiting
- Follow the [SECURITY_GUIDE.md](./SECURITY_GUIDE.md)

---

## 🎨 Frontend Questions

### How is Svelte different from React?

| Aspect | React | Svelte 5 |
|--------|-------|----------|
| **Reactivity** | `useState`, `useEffect` | `$state`, `$derived`, `$effect` |
| **Components** | JSX functions | `.svelte` files |
| **State** | Immutable updates | Mutable with `$state` |
| **Bundle size** | Runtime included | Compiled away |
| **Learning curve** | Steeper (hooks) | Gentler |

**Example comparison:**

**React:**
```jsx
function Counter() {
  const [count, setCount] = useState(0);
  const doubled = useMemo(() => count * 2, [count]);

  useEffect(() => {
    console.log('Count changed:', count);
  }, [count]);

  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

**Svelte 5:**
```svelte
<script lang="ts">
  let count = $state(0);
  let doubled = $derived(count * 2);

  $effect(() => {
    console.log('Count changed:', count);
  });
</script>

<button onclick={() => count++}>{count}</button>
```

### What are Svelte 5 Runes?

🆕 **NEW IN 2025:** Runes are Svelte 5's new reactivity primitives:

- `$state` - Reactive variable
- `$derived` - Computed value (like useMemo)
- `$effect` - Side effect (like useEffect)
- `$props` - Component props with types

⚠️ **IMPORTANT:** Old Svelte syntax (`$:`, `export let`) is deprecated in Svelte 5.

See [FRONTEND_ARCHITECTURE.md](./FRONTEND_ARCHITECTURE.md) for complete guide.

### How do I navigate between pages?

Use SvelteKit's file-based routing:

```
src/routes/
├── +page.svelte           # /
├── chat/
│   └── +page.svelte      # /chat
└── settings/
    └── +page.svelte      # /settings
```

**Programmatic navigation:**
```typescript
import { goto } from '$app/navigation';

goto('/chat');
```

**Link navigation:**
```svelte
<a href="/chat">Go to Chat</a>
```

### How do I share state between components?

Use Svelte stores:

```typescript
// src/lib/stores/user.ts
import { writable } from 'svelte/store';

export const user = writable<User | null>(null);
```

```svelte
<script lang="ts">
  import { user } from '$lib/stores/user';
</script>

{#if $user}
  <p>Welcome, {$user.name}!</p>
{/if}
```

### How do I call the backend API?

Use the API client functions:

```typescript
// src/lib/apis/chats.ts
export async function getChats() {
  const res = await fetch('/api/v1/chats', {
    headers: { 'Authorization': `Bearer ${localStorage.token}` }
  });
  return res.json();
}
```

```svelte
<script lang="ts">
  import { getChats } from '$lib/apis/chats';

  let chats = $state<Chat[]>([]);

  async function load() {
    chats = await getChats();
  }
</script>
```

---

## ⚙️ Backend Questions

### How do I add a new API endpoint?

1. **Create route in router:**

```python
# backend/open_webui/routers/example.py
from fastapi import APIRouter, Depends

router = APIRouter(prefix="/example", tags=["example"])

@router.get("/")
async def get_examples(user=Depends(get_verified_user)):
    return {"message": "Hello!"}
```

2. **Register router:**

```python
# backend/open_webui/main.py
from open_webui.routers import example

app.include_router(example.router)
```

3. **Test:**
```bash
curl http://localhost:8080/api/v1/example \
  -H "Authorization: Bearer YOUR_TOKEN"
```

See [HOW_TO_GUIDE.md](./HOW_TO_GUIDE.md#add-a-new-api-endpoint).

### How does authentication work?

1. **User signs in:** `POST /api/v1/auth/signin`
2. **Server generates JWT:** Signed token with user ID
3. **Token stored:** httpOnly cookie (not localStorage for security)
4. **Subsequent requests:** Token sent in `Authorization: Bearer <token>` header
5. **Server verifies:** `get_verified_user()` dependency checks token

See [SECURITY_GUIDE.md](./SECURITY_GUIDE.md) for details.

### What's the difference between SQLAlchemy 1.x and 2.0?

🆕 **Open WebUI uses SQLAlchemy 2.0** (released 2023):

| Aspect | SQLAlchemy 1.x | SQLAlchemy 2.0 |
|--------|----------------|----------------|
| **Query API** | `session.query(Model)` | `session.execute(select(Model))` |
| **Results** | `.all()`, `.first()` | `.scalars().all()` |
| **Async** | Limited | Full support |
| **Type hints** | Poor | Excellent |

**Example:**

❌ **Old 1.x style:**
```python
users = session.query(User).filter(User.age > 18).all()
```

✅ **CURRENT 2.0 style:**
```python
stmt = select(User).where(User.age > 18)
users = session.scalars(stmt).all()
```

See [DATABASE_ARCHITECTURE.md](./DATABASE_ARCHITECTURE.md).

### How do I add a database table?

1. **Define model:**

```python
# backend/open_webui/models/example.py
from sqlalchemy import Column, String, BigInteger

class Example(Base):
    __tablename__ = "example"

    id = Column(String, primary_key=True)
    name = Column(String, nullable=False)
    created_at = Column(BigInteger, nullable=False)
```

2. **Create migration:**

```bash
# From project root
alembic -c backend/open_webui/alembic.ini revision -m "add example table"
```

3. **Edit migration:**

```python
# backend/open_webui/migrations/versions/xxxx_add_example_table.py
def upgrade():
    op.create_table('example',
        sa.Column('id', sa.String(), nullable=False),
        sa.Column('name', sa.String(), nullable=False),
        sa.Column('created_at', sa.BigInteger(), nullable=False),
        sa.PrimaryKeyConstraint('id')
    )

def downgrade():
    op.drop_table('example')
```

4. **Run migration:**

```bash
# From project root
alembic -c backend/open_webui/alembic.ini upgrade head
```

### How do I stream LLM responses?

Use Server-Sent Events (SSE):

```python
# backend/open_webui/routers/chat.py
from fastapi.responses import StreamingResponse

@router.post("/stream")
async def stream_chat(request: ChatRequest):
    async def generate():
        for chunk in llm_client.stream(request.message):
            yield f"data: {json.dumps({'content': chunk})}\n\n"

    return StreamingResponse(generate(), media_type="text/event-stream")
```

**Frontend:**
```typescript
const eventSource = new EventSource('/api/v1/chat/stream');

eventSource.onmessage = (event) => {
  const data = JSON.parse(event.data);
  console.log(data.content);
};
```

---

## 🗄️ Database Questions

### Should I use SQLite or PostgreSQL?

| Use Case | Database |
|----------|----------|
| **Development** | SQLite (easier setup) |
| **Production** | PostgreSQL (better concurrency) |
| **Multi-user** | PostgreSQL |
| **Single-user self-hosted** | Either works |

**Why PostgreSQL for production?**
- Better concurrent write performance
- No "database locked" errors
- Better full-text search
- More scalable

### How do I switch to PostgreSQL?

1. **Install PostgreSQL:**
```bash
# macOS
brew install postgresql@14
brew services start postgresql@14

# Ubuntu
sudo apt-get install postgresql-14
```

2. **Create database:**
```bash
createdb openwebui
```

3. **Set environment variable:**
```bash
export DATABASE_URL="postgresql://user:password@localhost/openwebui"
```

4. **Run migrations:**
```bash
# From project root
alembic -c backend/open_webui/alembic.ini upgrade head
```

### How do I reset the database?

⚠️ **WARNING: This deletes all data!**

```bash
# From project root

# Drop all tables
alembic -c backend/open_webui/alembic.ini downgrade base

# Recreate tables
alembic -c backend/open_webui/alembic.ini upgrade head
```

---

## 🤖 AI/ML Questions

### What LLM providers are supported?

- **Ollama** - Local models (llama3, mistral, etc.)
- **OpenAI** - GPT-4, GPT-3.5
- **Anthropic** - Claude models
- **Azure OpenAI** - Enterprise OpenAI
- **Custom OpenAI-compatible APIs**

### How does RAG work in Open WebUI?

1. **User uploads document** (PDF, TXT, etc.)
2. **Document is chunked** into smaller pieces
3. **Chunks are embedded** using sentence transformers
4. **Embeddings stored** in ChromaDB (vector database)
5. **User asks question** about the document
6. **Relevant chunks retrieved** via similarity search
7. **Chunks + question sent to LLM** as context
8. **LLM generates answer** using retrieved context

See [INTEGRATION_GUIDE.md](./INTEGRATION_GUIDE.md#chromadb-vector-database).

### How do I add a new LLM provider?

1. **Create provider client:**

```python
# backend/open_webui/llm/providers/custom.py
class CustomLLMProvider:
    def __init__(self, api_key: str):
        self.api_key = api_key

    async def chat(self, messages: list) -> str:
        # Implement API call
        pass

    async def stream(self, messages: list):
        # Implement streaming
        pass
```

2. **Register provider:**

```python
# backend/open_webui/llm/registry.py
PROVIDERS = {
    'ollama': OllamaProvider,
    'openai': OpenAIProvider,
    'custom': CustomLLMProvider,  # Add here
}
```

3. **Add configuration UI** in frontend settings

---

## 🧪 Testing Questions

### How do I run tests?

**Frontend unit tests:**
```bash
npm run test
```

**Backend unit tests:**
```bash
cd backend
pytest
```

**E2E tests:**
```bash
npm run cy:open
```

### How do I write a unit test?

**Frontend (Vitest):**
```typescript
// src/lib/utils/format.test.ts
import { describe, it, expect } from 'vitest';
import { formatDate } from './format';

describe('formatDate', () => {
  it('formats timestamp correctly', () => {
    expect(formatDate(1700000000)).toBe('Nov 14, 2023');
  });
});
```

**Backend (Pytest):**
```python
# backend/tests/test_chats.py
def test_create_chat(client, auth_headers):
    response = client.post(
        "/api/v1/chats/new",
        json={"title": "Test"},
        headers=auth_headers
    )
    assert response.status_code == 200
```

See [TESTING_GUIDE.md](./TESTING_GUIDE.md).

---

## 🐛 Troubleshooting

### "Cannot connect to backend"

**Symptoms:** Frontend shows "Cannot connect to server"

**Solutions:**
1. Check backend is running: `http://localhost:8080/health`
2. Check proxy in `vite.config.ts`:
   ```typescript
   server: {
     proxy: {
       '/api': 'http://localhost:8080'
     }
   }
   ```
3. Check CORS settings in `backend/open_webui/main.py`

### "Database locked" (SQLite)

**Symptoms:** Error: `database is locked`

**Solutions:**
1. Close other connections (stop duplicate backend instances)
2. Increase timeout:
   ```python
   engine = create_engine('sqlite:///data.db', connect_args={'timeout': 30})
   ```
3. Switch to PostgreSQL for production

### "Module not found"

**Frontend:**
```bash
rm -rf node_modules package-lock.json
npm install
```

**Backend:**
```bash
pip uninstall open-webui
pip install -e .
```

### Build fails with TypeScript errors

```bash
# Check for type errors
npm run check

# If using VS Code, reload window
# Cmd+Shift+P → "Reload Window"
```

### Backend hot reload not working

Make sure you're running with `--reload`:

```bash
uvicorn open_webui.main:app --reload --port 8080
```

### ChromaDB errors on Apple Silicon

ChromaDB may have issues on M1/M2 Macs:

```bash
# Use x86 emulation
arch -x86_64 pip install chromadb
```

---

## 🚀 Deployment Questions

### How do I deploy to production?

See the official deployment guide: https://docs.openwebui.com/deployment

**Quick overview:**
1. **Docker (recommended):**
   ```bash
   docker pull ghcr.io/open-webui/open-webui:main
   docker run -p 8080:8080 -v open-webui:/app/backend/data ghcr.io/open-webui/open-webui:main
   ```

2. **Manual deployment:**
   - Build frontend: `npm run build`
   - Copy `build/` to server
   - Run backend with production ASGI server (e.g., Gunicorn + Uvicorn)
   - Set up reverse proxy (nginx)
   - Configure SSL certificates

### What environment variables do I need?

**Required:**
- `DATABASE_URL` - Database connection string
- `JWT_SECRET` - Secret for signing tokens

**Optional:**
- `OLLAMA_BASE_URL` - Ollama API endpoint
- `OPENAI_API_KEY` - OpenAI API key
- `WEBUI_SECRET_KEY` - Session encryption key

**Example `.env`:**
```bash
DATABASE_URL=postgresql://user:pass@localhost/openwebui
JWT_SECRET=your-secret-key-here
OLLAMA_BASE_URL=http://localhost:11434
```

### How do I enable HTTPS?

Use a reverse proxy like nginx:

```nginx
server {
    listen 443 ssl;
    server_name openwebui.example.com;

    ssl_certificate /path/to/cert.pem;
    ssl_certificate_key /path/to/key.pem;

    location / {
        proxy_pass http://localhost:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

---

## 🎓 Learning Questions

### I'm new to Svelte. Where should I start?

1. **Official Svelte Tutorial:** https://svelte.dev/tutorial
2. **Read [FRONTEND_ARCHITECTURE.md](./FRONTEND_ARCHITECTURE.md)**
3. **Complete beginner exercises in [EXERCISES.md](./EXERCISES.md)**
4. **Study existing components** in `src/lib/components/`

### I'm coming from React. What's different?

| React | Svelte 5 |
|-------|----------|
| `useState` | `$state` |
| `useMemo` | `$derived` |
| `useEffect` | `$effect` |
| `props.value` | `$props().value` |
| `.map()` in JSX | `{#each}` block |
| `&&` for conditionals | `{#if}` block |

See comparison examples in [TECH_STACK_GUIDE.md](./TECH_STACK_GUIDE.md).

### I'm coming from Django/Flask. What's different?

| Django/Flask | FastAPI |
|--------------|---------|
| View functions | Async functions |
| Decorators | Decorators (similar!) |
| Django ORM | SQLAlchemy |
| Manual validation | Pydantic (automatic) |
| Templates | JSON responses only |
| URL patterns | Path operations |

FastAPI will feel familiar! Main differences:
- Everything is async by default
- Automatic OpenAPI docs
- Type hints are required and validated

### What should I build to practice?

See [EXERCISES.md](./EXERCISES.md) for 10 progressive exercises from beginner to advanced.

**Project ideas:**
- Add dark mode toggle
- Build a chat export feature (PDF/Markdown)
- Create a plugin system
- Add keyboard shortcuts
- Implement search functionality

---

## 🤝 Contributing Questions

### How do I contribute?

See [FIRST_CONTRIBUTIONS.md](./FIRST_CONTRIBUTIONS.md) for complete guide.

**Quick steps:**
1. Fork the repository
2. Find a "good first issue"
3. Create a feature branch
4. Make your changes
5. Submit a pull request

### What makes a good first contribution?

- Documentation fixes
- UI/UX improvements
- Adding tooltips or help text
- Fixing console warnings
- Adding unit tests

### How long does PR review take?

Usually 1-3 days, but depends on:
- Maintainer availability
- Complexity of changes
- How well PR follows guidelines

**Tip:** Smaller, focused PRs get reviewed faster!

---

## ❓ Still Have Questions?

- **Discord:** https://discord.gg/open-webui (#help channel)
- **GitHub Discussions:** https://github.com/open-webui/open-webui/discussions
- **GitHub Issues:** https://github.com/open-webui/open-webui/issues

---

**Last updated:** November 18, 2025
