# Hands-On Exercises

**Practical exercises to master Open WebUI development.**

**Documented:** November 18, 2025

---

## 🎯 How to Use These Exercises

1. **Start with Beginner** exercises to build foundational skills
2. **Progress to Intermediate** once comfortable with the stack
3. **Tackle Advanced** exercises to master complex patterns
4. **Check Solutions** in the `solutions/` branch (coming soon)

**Prerequisites:**
- Completed [GETTING_STARTED.md](./GETTING_STARTED.md)
- Development environment running
- Basic understanding of Svelte and FastAPI

---

## 🟢 Beginner Exercises

### Exercise 1: Add a Character Counter to Chat Input

**Goal:** Practice Svelte 5 Runes and component props.

**Task:**
1. Open `src/lib/components/chat/MessageInput.svelte`
2. Add a character counter that shows: `{current}/{max}` characters
3. Use `$derived` to calculate the count
4. Display in gray when under 100 chars, yellow when 100-200, red when over 200

**Hints:**
```svelte
<script lang="ts">
  let message = $state('');
  let charCount = $derived(message.length);
  let charColor = $derived(
    charCount < 100 ? 'text-gray-500' :
    charCount < 200 ? 'text-yellow-500' : 'text-red-500'
  );
</script>

<div class={charColor}>
  {charCount}/1000
</div>
```

**Learning Outcomes:**
- ✅ Svelte 5 `$state` and `$derived`
- ✅ Reactive color changes
- ✅ Component editing

---

### Exercise 2: Create a "Last Updated" Timestamp

**Goal:** Display relative timestamps for chats.

**Task:**
1. Create `src/lib/utils/formatRelativeTime.ts`
2. Write a function that returns "2 minutes ago", "3 hours ago", etc.
3. Use it in the chat list to show last message time

**Example:**
```typescript
// src/lib/utils/formatRelativeTime.ts
export function formatRelativeTime(timestamp: number): string {
  const now = Date.now();
  const diff = Math.floor((now - timestamp * 1000) / 1000); // seconds

  if (diff < 60) return `${diff} seconds ago`;
  if (diff < 3600) return `${Math.floor(diff / 60)} minutes ago`;
  if (diff < 86400) return `${Math.floor(diff / 3600)} hours ago`;
  return `${Math.floor(diff / 86400)} days ago`;
}
```

**Use in component:**
```svelte
<script lang="ts">
  import { formatRelativeTime } from '$lib/utils/formatRelativeTime';

  let { chat } = $props<{ chat: Chat }>();
</script>

<div class="text-xs text-gray-500">
  {formatRelativeTime(chat.updated_at)}
</div>
```

**Learning Outcomes:**
- ✅ Create utility functions
- ✅ Work with timestamps
- ✅ Import and use custom utilities

---

### Exercise 3: Add a Simple REST Endpoint

**Goal:** Create a backend API endpoint.

**Task:**
1. Open `backend/open_webui/routers/chats.py`
2. Add a `GET /api/v1/chats/{chat_id}/stats` endpoint
3. Return: `{ message_count: int, created_at: int, last_message_at: int }`

**Hints:**
```python
# backend/open_webui/routers/chats.py
@router.get("/{chat_id}/stats")
async def get_chat_stats(
    chat_id: str,
    user=Depends(get_verified_user)
):
    chat = Chats.get_chat_by_id_and_user_id(chat_id, user.id)
    if not chat:
        raise HTTPException(status_code=404, detail="Chat not found")

    # Parse chat.chat JSON to count messages
    messages = chat.chat.get("messages", [])

    return {
        "message_count": len(messages),
        "created_at": chat.created_at,
        "last_message_at": chat.updated_at
    }
```

**Test:**
```bash
curl http://localhost:8080/api/v1/chats/YOUR_CHAT_ID/stats \
  -H "Authorization: Bearer YOUR_TOKEN"
```

**Learning Outcomes:**
- ✅ Create FastAPI endpoints
- ✅ Use dependency injection
- ✅ Query database models
- ✅ Return JSON responses

---

## 🟡 Intermediate Exercises

### Exercise 4: Build a "Favorite Chats" Feature

**Goal:** Add persistence and UI for favoriting chats.

**Tasks:**
1. **Backend:** Add `is_favorite` boolean column to `chat` table
2. **Migration:** Create Alembic migration
3. **API:** Add `POST /api/v1/chats/{chat_id}/favorite` endpoint
4. **Frontend:** Add star icon to chat list
5. **Frontend:** Filter to show only favorites

**Database Migration:**
```python
# backend/alembic/versions/xxxx_add_favorite_column.py
def upgrade():
    op.add_column('chat', sa.Column('is_favorite', sa.Boolean(), server_default='0'))

def downgrade():
    op.drop_column('chat', 'is_favorite')
```

**Backend Endpoint:**
```python
@router.post("/{chat_id}/favorite")
async def toggle_favorite(
    chat_id: str,
    user=Depends(get_verified_user)
):
    chat = Chats.get_chat_by_id_and_user_id(chat_id, user.id)
    if not chat:
        raise HTTPException(status_code=404)

    chat.is_favorite = not chat.is_favorite
    db.commit()

    return {"is_favorite": chat.is_favorite}
```

**Frontend Store:**
```typescript
// src/lib/stores/chats.ts
export async function toggleFavorite(chatId: string) {
  const res = await fetch(`/api/v1/chats/${chatId}/favorite`, {
    method: 'POST',
    headers: { 'Authorization': `Bearer ${localStorage.token}` }
  });

  const data = await res.json();
  // Update local state
  chats.update(list =>
    list.map(c => c.id === chatId ? {...c, is_favorite: data.is_favorite} : c)
  );
}
```

**Learning Outcomes:**
- ✅ Database migrations
- ✅ Full-stack feature implementation
- ✅ State management
- ✅ CRUD operations

---

### Exercise 5: Implement Client-Side Search

**Goal:** Filter chats by title or content.

**Task:**
1. Add search input to chat sidebar
2. Filter chats using `$derived` based on search term
3. Highlight matching text

**Implementation:**
```svelte
<script lang="ts">
  let searchTerm = $state('');
  let chats = $state<Chat[]>([]);

  let filteredChats = $derived(
    chats.filter(chat =>
      chat.title.toLowerCase().includes(searchTerm.toLowerCase()) ||
      JSON.stringify(chat.chat).toLowerCase().includes(searchTerm.toLowerCase())
    )
  );
</script>

<input
  type="text"
  bind:value={searchTerm}
  placeholder="Search chats..."
  class="px-4 py-2 border rounded"
/>

{#each filteredChats as chat}
  <ChatItem {chat} {searchTerm} />
{/each}
```

**Learning Outcomes:**
- ✅ Client-side filtering
- ✅ Reactive derived state
- ✅ Text search algorithms

---

### Exercise 6: Add Request/Response Logging Middleware

**Goal:** Log all API requests for debugging.

**Task:**
1. Create middleware in `backend/open_webui/middleware/logging.py`
2. Log: method, path, status code, duration
3. Register middleware in `main.py`

**Implementation:**
```python
# backend/open_webui/middleware/logging.py
import time
import logging
from fastapi import Request

logger = logging.getLogger(__name__)

async def log_requests(request: Request, call_next):
    start_time = time.time()

    response = await call_next(request)

    duration = time.time() - start_time
    logger.info(
        f"{request.method} {request.url.path} "
        f"- {response.status_code} - {duration:.2f}s"
    )

    return response
```

**Register in `main.py`:**
```python
from open_webui.middleware.logging import log_requests

app.middleware("http")(log_requests)
```

**Learning Outcomes:**
- ✅ FastAPI middleware
- ✅ Request/response interception
- ✅ Logging best practices

---

## 🔴 Advanced Exercises

### Exercise 7: Implement Server-Sent Events for Live Updates

**Goal:** Push chat updates to all connected clients in real-time.

**Task:**
1. Create SSE endpoint: `GET /api/v1/events/subscribe`
2. Broadcast when new message is added to any chat
3. Subscribe from frontend and update UI

**Backend:**
```python
# backend/open_webui/routers/events.py
from fastapi import APIRouter
from sse_starlette.sse import EventSourceResponse
import asyncio

router = APIRouter(prefix="/events", tags=["events"])

# Global event queue
event_queue = asyncio.Queue()

@router.get("/subscribe")
async def subscribe(user=Depends(get_verified_user)):
    async def event_generator():
        while True:
            event = await event_queue.get()
            if event['user_id'] == user.id:
                yield {
                    "event": event['type'],
                    "data": json.dumps(event['data'])
                }

    return EventSourceResponse(event_generator())

# Call this when message is created
async def broadcast_message(user_id: str, chat_id: str, message: dict):
    await event_queue.put({
        'user_id': user_id,
        'type': 'new_message',
        'data': {'chat_id': chat_id, 'message': message}
    })
```

**Frontend:**
```typescript
// src/lib/services/events.ts
export function subscribeToEvents(onMessage: (event: any) => void) {
  const eventSource = new EventSource('/api/v1/events/subscribe');

  eventSource.addEventListener('new_message', (e) => {
    const data = JSON.parse(e.data);
    onMessage(data);
  });

  return () => eventSource.close();
}
```

**Learning Outcomes:**
- ✅ Server-Sent Events
- ✅ Real-time communication
- ✅ Event broadcasting
- ✅ Advanced async patterns

---

### Exercise 8: Implement RAG Document Upload

**Goal:** Allow users to upload PDFs and query them with RAG.

**Tasks:**
1. Create file upload endpoint
2. Extract text from PDF using `PyPDF2`
3. Chunk text and store in ChromaDB
4. Query with user's question

**Backend:**
```python
# backend/open_webui/routers/knowledge.py
from PyPDF2 import PdfReader
from chromadb import Client

@router.post("/upload")
async def upload_document(
    file: UploadFile,
    user=Depends(get_verified_user)
):
    # Read PDF
    pdf = PdfReader(file.file)
    text = ""
    for page in pdf.pages:
        text += page.extract_text()

    # Chunk text (simple strategy: 500 chars)
    chunks = [text[i:i+500] for i in range(0, len(text), 500)]

    # Store in ChromaDB
    collection = chroma_client.get_or_create_collection("documents")
    collection.add(
        documents=chunks,
        ids=[f"{user.id}_{file.filename}_{i}" for i in range(len(chunks))],
        metadatas=[{"user_id": user.id, "filename": file.filename}] * len(chunks)
    )

    return {"chunks": len(chunks)}

@router.post("/query")
async def query_documents(
    query: str,
    user=Depends(get_verified_user)
):
    collection = chroma_client.get_collection("documents")
    results = collection.query(
        query_texts=[query],
        n_results=5,
        where={"user_id": user.id}
    )

    # Use retrieved context with LLM
    context = "\n\n".join(results['documents'][0])
    prompt = f"Context:\n{context}\n\nQuestion: {query}"

    # Send to LLM (implementation depends on your setup)
    answer = await call_llm(prompt)

    return {"answer": answer, "sources": results['documents'][0]}
```

**Learning Outcomes:**
- ✅ File upload handling
- ✅ PDF text extraction
- ✅ Vector database operations
- ✅ RAG pipeline implementation

---

### Exercise 9: Add OAuth2 Google Sign-In

**Goal:** Implement social authentication.

**Tasks:**
1. Register app with Google Cloud Console
2. Add OAuth2 flow in backend
3. Create "Sign in with Google" button

**Backend:**
```python
# backend/open_webui/routers/auth.py
from authlib.integrations.starlette_client import OAuth

oauth = OAuth()
oauth.register(
    name='google',
    client_id=os.getenv('GOOGLE_CLIENT_ID'),
    client_secret=os.getenv('GOOGLE_CLIENT_SECRET'),
    server_metadata_url='https://accounts.google.com/.well-known/openid-configuration',
    client_kwargs={'scope': 'openid email profile'}
)

@router.get("/google/login")
async def google_login(request: Request):
    redirect_uri = request.url_for('google_callback')
    return await oauth.google.authorize_redirect(request, redirect_uri)

@router.get("/google/callback")
async def google_callback(request: Request):
    token = await oauth.google.authorize_access_token(request)
    user_info = token['userinfo']

    # Create or get user
    user = Users.get_user_by_email(user_info['email'])
    if not user:
        user = Users.insert_new_user({
            'email': user_info['email'],
            'name': user_info['name'],
            'profile_image_url': user_info['picture'],
            'role': 'user'
        })

    # Generate JWT
    token = create_token(data={'id': user.id})

    return RedirectResponse(url=f'/?token={token}')
```

**Frontend:**
```svelte
<a href="/api/v1/auth/google/login" class="btn">
  <img src="/google-icon.svg" alt="Google" />
  Sign in with Google
</a>
```

**Learning Outcomes:**
- ✅ OAuth2 flow
- ✅ Third-party authentication
- ✅ Token exchange
- ✅ User provisioning

---

### Exercise 10: Build a Plugin System

**Goal:** Allow users to write custom JavaScript plugins.

**Tasks:**
1. Create plugin API interface
2. Load plugins from `/plugins` directory
3. Provide hooks: `onMessage`, `onResponse`, `onRender`

**Plugin Interface:**
```typescript
// src/lib/types/plugin.ts
export interface Plugin {
  name: string;
  version: string;

  onMessage?: (message: string) => string;  // Transform before send
  onResponse?: (response: string) => string; // Transform after receive
  onRender?: (element: HTMLElement) => void; // Modify DOM
}
```

**Plugin Loader:**
```typescript
// src/lib/plugins/loader.ts
export async function loadPlugins(): Promise<Plugin[]> {
  const pluginModules = import.meta.glob('/plugins/*.ts');
  const plugins: Plugin[] = [];

  for (const path in pluginModules) {
    const module = await pluginModules[path]() as any;
    if (module.default) {
      plugins.push(module.default);
    }
  }

  return plugins;
}

export function applyPlugins(plugins: Plugin[], hook: keyof Plugin, ...args: any[]) {
  let result = args[0];

  for (const plugin of plugins) {
    const fn = plugin[hook];
    if (typeof fn === 'function') {
      result = fn(result);
    }
  }

  return result;
}
```

**Example Plugin:**
```typescript
// plugins/emoji-replacer.ts
import type { Plugin } from '$lib/types/plugin';

export default {
  name: 'Emoji Replacer',
  version: '1.0.0',

  onMessage(message: string): string {
    return message
      .replace(':)', '😊')
      .replace(':D', '😄')
      .replace(':(', '😢');
  }
} satisfies Plugin;
```

**Use in Chat:**
```typescript
import { loadPlugins, applyPlugins } from '$lib/plugins/loader';

let plugins = $state<Plugin[]>([]);

$effect(() => {
  loadPlugins().then(p => plugins = p);
});

async function sendMessage(message: string) {
  const transformed = applyPlugins(plugins, 'onMessage', message);
  await sendToAPI(transformed);
}
```

**Learning Outcomes:**
- ✅ Plugin architecture
- ✅ Dynamic imports
- ✅ Hook system design
- ✅ Extensibility patterns

---

## 🎓 Learning Tips

1. **Start Small:** Don't skip beginner exercises
2. **Read Code:** Study existing implementations before writing
3. **Use DevTools:** Browser console and Network tab are your friends
4. **Break It:** Intentionally break things to understand how they work
5. **Git Branches:** Create a branch for each exercise
6. **Ask Questions:** Join the Discord community

---

## 📚 Next Steps

After completing exercises:
1. Read [FIRST_CONTRIBUTIONS.md](./FIRST_CONTRIBUTIONS.md) to contribute
2. Build your own features
3. Join the Open WebUI community
4. Share your learnings!

---

**Last updated:** November 18, 2025
