# Frontend Architecture

**Deep dive into Open WebUI's frontend: Svelte 5, SvelteKit, state management, and component patterns.**

**Estimated reading time:** 2-3 hours
**Prerequisites:** [TECH_STACK_GUIDE.md](./TECH_STACK_GUIDE.md), [PROJECT_STRUCTURE.md](./PROJECT_STRUCTURE.md)
**Documented:** November 18, 2025

---

## 📋 Table of Contents

1. [Overview](#overview)
2. [Svelte 5 Fundamentals](#svelte-5-fundamentals)
3. [SvelteKit Routing](#sveltekit-routing)
4. [State Management](#state-management)
5. [Component Architecture](#component-architecture)
6. [API Integration](#api-integration)
7. [Real-time Features](#real-time-features)
8. [Special Features](#special-features)
9. [Performance Optimization](#performance-optimization)
10. [Best Practices](#best-practices)

---

## Overview

### Frontend Tech Stack

```
┌────────────────────────────────────┐
│      Svelte 5 (v5.0.0)             │  Component framework
├────────────────────────────────────┤
│      SvelteKit 2 (v2.5.27)         │  Meta-framework
├────────────────────────────────────┤
│      TypeScript 5.5                │  Type safety
├────────────────────────────────────┤
│      Tailwind CSS v4               │  Styling
├────────────────────────────────────┤
│      Vite 5                        │  Build tool
└────────────────────────────────────┘
```

### Directory Structure Recap

```
src/
├── lib/
│   ├── components/        # Reusable Svelte components
│   ├── stores/            # Global state (Svelte stores)
│   ├── apis/              # API client functions
│   ├── types/             # TypeScript types
│   ├── utils/             # Helper functions
│   └── workers/           # Web Workers
└── routes/                # SvelteKit file-based routing
    ├── (app)/            # Main application
    ├── auth/             # Authentication
    └── ...
```

---

## Svelte 5 Fundamentals

### 🆕 The Runes Revolution

Svelte 5 introduces **Runes** - a new way to manage reactivity.

**⚠️ Critical:** Old Svelte 3/4 patterns (`$:`, reactive declarations) are outdated!

### $state - Reactive Variables

✅ **CURRENT (Svelte 5):**
```svelte
<script lang="ts">
  let count = $state(0);
  let user = $state<User | null>(null);

  function increment() {
    count++;  // Mutation is reactive!
  }
</script>

<button onclick={increment}>{count}</button>
```

❌ **OUTDATED (Svelte 3/4):**
```svelte
<script>
  let count = 0;  // Just a variable, no $state
</script>
```

#### 🧠 Mental Model: $state()

Think of `$state()` as marking a variable as "reactive":

```javascript
// Without $state - Not reactive
let count = 0;
count++;  // UI won't update

// With $state - Reactive
let count = $state(0);
count++;  // UI updates automatically
```

#### Deep Reactivity

```svelte
<script lang="ts">
  let todos = $state([
    { id: 1, text: 'Learn Svelte', done: false }
  ]);

  function toggleTodo(id: number) {
    const todo = todos.find(t => t.id === id);
    if (todo) {
      todo.done = !todo.done;  // Deep mutation is reactive!
    }
  }
</script>
```

🎯 **Remember:** Svelte 5 tracks mutations at any depth automatically.

---

### $derived - Computed Values

```svelte
<script lang="ts">
  let count = $state(0);
  let doubled = $derived(count * 2);
  let tripled = $derived(count * 3);

  // Derived from multiple sources
  let items = $state([1, 2, 3]);
  let total = $derived(items.reduce((a, b) => a + b, 0));
</script>

<p>Count: {count}</p>
<p>Doubled: {doubled}</p>
<p>Total: {total}</p>
```

#### 🌉 For React Developers

| React | Svelte 5 |
|-------|----------|
| `useMemo(() => count * 2, [count])` | `$derived(count * 2)` |
| `const doubled = count * 2` (recomputes every render) | `$derived(count * 2)` (caches) |

🎯 **Remember:** `$derived` is memoized - only recomputes when dependencies change.

---

### $effect - Side Effects

```svelte
<script lang="ts">
  let count = $state(0);

  // Runs when count changes
  $effect(() => {
    console.log('Count is now:', count);
    document.title = `Count: ${count}`;
  });

  // Cleanup function
  $effect(() => {
    const interval = setInterval(() => {
      console.log('Tick');
    }, 1000);

    return () => clearInterval(interval);  // Cleanup
  });
</script>
```

#### 🌉 For React Developers

| React | Svelte 5 |
|-------|----------|
| `useEffect(() => {...}, [count])` | `$effect(() => { use count })` |
| `useEffect(() => { return cleanup }, [])` | `$effect(() => { return cleanup })` |

#### ⚠️ Common Pitfall: Infinite Loops

```svelte
<script lang="ts">
  let count = $state(0);

  // ❌ INFINITE LOOP!
  $effect(() => {
    count++;  // Don't mutate state in effects!
  });

  // ✅ CORRECT
  $effect(() => {
    console.log(count);  // Just read, don't mutate
  });
</script>
```

---

### $props - Component Props

```svelte
<!-- ParentComponent.svelte -->
<ChildComponent name="John" age={30} />

<!-- ChildComponent.svelte -->
<script lang="ts">
  interface Props {
    name: string;
    age: number;
    optional?: string;
  }

  let { name, age, optional = 'default' }: Props = $props();
</script>

<p>Name: {name}, Age: {age}</p>
```

#### Reactive Props

```svelte
<script lang="ts">
  let props = $props<Props>();

  // Derived from props
  let uppercaseName = $derived(props.name.toUpperCase());
</script>

<p>{uppercaseName}</p>
```

#### ⚠️ Common Pitfall: Destructuring Loses Reactivity

```svelte
<script lang="ts">
  // ❌ BAD - Loses reactivity!
  let { name } = $props();
  // name is now just a value, not reactive

  // ✅ GOOD - Keeps reactivity
  let props = $props();
  // Use: props.name (reactive)
</script>
```

---

### Event Handlers (Svelte 5)

✅ **CURRENT:**
```svelte
<button onclick={handleClick}>Click me</button>
<input oninput={(e) => handleInput(e.target.value)} />
<form onsubmit={handleSubmit}>...</form>
```

❌ **OUTDATED:**
```svelte
<button on:click={handleClick}>Click me</button>
<input on:input={(e) => handleInput(e.target.value)} />
```

🎯 **Remember:** Svelte 5 uses lowercase `onclick`, `oninput`, etc. (like vanilla JS)

---

### Snippets (New in Svelte 5)

Reusable template fragments:

```svelte
<script lang="ts">
  let items = $state(['Apple', 'Banana', 'Cherry']);
</script>

{#snippet listItem(item: string)}
  <li class="font-bold">{item}</li>
{/snippet}

<ul>
  {#each items as item}
    {@render listItem(item)}
  {/each}
</ul>
```

---

## SvelteKit Routing

### File-Based Routing

```
src/routes/
├── +page.svelte              →  /
├── about/+page.svelte        →  /about
├── blog/
│   ├── +page.svelte          →  /blog
│   └── [slug]/
│       └── +page.svelte      →  /blog/:slug
└── (app)/                    ←  Route group (no URL)
    └── dashboard/
        └── +page.svelte      →  /dashboard
```

### Special Files

| File | Purpose | When it runs |
|------|---------|-------------|
| `+page.svelte` | Page component | Always |
| `+page.ts` | Load data | Server + Client |
| `+page.server.ts` | Server-only load | Server only |
| `+layout.svelte` | Layout wrapper | Always |
| `+layout.ts` | Layout data | Server + Client |
| `+server.ts` | API endpoint | Server only |
| `+error.svelte` | Error page | On error |

### Loading Data

#### Universal Load (+page.ts)

Runs on both server and client:

```typescript
// src/routes/blog/[slug]/+page.ts
import type { PageLoad } from './$types';

export const load: PageLoad = async ({ params, fetch }) => {
  const res = await fetch(`/api/posts/${params.slug}`);
  const post = await res.json();

  return { post };
};
```

```svelte
<!-- src/routes/blog/[slug]/+page.svelte -->
<script lang="ts">
  import type { PageData } from './$types';

  let { data }: { data: PageData } = $props();
</script>

<h1>{data.post.title}</h1>
<div>{@html data.post.content}</div>
```

#### Server-Only Load (+page.server.ts)

```typescript
// src/routes/admin/+page.server.ts
import type { PageServerLoad } from './$types';
import { db } from '$lib/server/database';

export const load: PageServerLoad = async ({ locals }) => {
  // Can use server-only code
  const users = await db.query('SELECT * FROM users');

  return { users };
};
```

🎯 **Remember:**
- `+page.ts` - Universal (can run on client)
- `+page.server.ts` - Server-only (can use secrets, databases directly)

---

### Layouts

Shared layouts for multiple pages:

```svelte
<!-- src/routes/(app)/+layout.svelte -->
<script lang="ts">
  import Sidebar from '$lib/components/layout/Sidebar.svelte';
  import Navbar from '$lib/components/layout/Navbar.svelte';
</script>

<div class="app-container">
  <Sidebar />
  <main>
    <Navbar />
    <slot />  <!-- Child pages render here -->
  </main>
</div>
```

#### Nested Layouts

```
routes/(app)/
├── +layout.svelte           ← Outer layout
├── dashboard/
│   ├── +layout.svelte       ← Inner layout
│   └── +page.svelte         ← Page (uses both layouts)
```

---

### Navigation

```svelte
<script lang="ts">
  import { goto } from '$app/navigation';
  import { page } from '$app/stores';

  // Programmatic navigation
  function navigate() {
    goto('/dashboard');
  }

  // Current route info
  console.log($page.url.pathname);  // "/dashboard"
  console.log($page.params);        // { id: '123' }
  console.log($page.data);          // Data from load function
</script>

<!-- Declarative navigation -->
<a href="/dashboard">Dashboard</a>
```

#### Prefetching

```svelte
<!-- Prefetch on hover -->
<a href="/blog/post-1" data-sveltekit-preload-data="hover">
  Read article
</a>

<!-- Prefetch immediately -->
<a href="/dashboard" data-sveltekit-preload-data>
  Dashboard
</a>
```

---

## State Management

### Global Stores

**Location:** [src/lib/stores/index.ts](../../src/lib/stores/index.ts)

#### Creating Stores

```typescript
import { writable, derived, readonly } from 'svelte/store';

// Writable store (can be updated)
export const user = writable<User | null>(null);

// Derived store (computed from other stores)
export const isAuthenticated = derived(
  user,
  ($user) => $user !== null
);

// Readonly store (external cannot update)
const _config = writable<Config>({});
export const config = readonly(_config);
```

#### Using Stores in Components

```svelte
<script lang="ts">
  import { user, chats } from '$lib/stores';

  // Read store value with $ prefix
  console.log($user);

  // Update store
  user.set({ id: '1', name: 'John' });

  // Or use update
  user.update(u => ({ ...u, name: 'Jane' }));

  // Subscribe (rarely needed - use $ instead)
  const unsubscribe = user.subscribe(value => {
    console.log('User changed:', value);
  });
</script>

{#if $user}
  <p>Welcome, {$user.name}!</p>
{/if}
```

#### Open WebUI's Store Structure

**File:** [src/lib/stores/index.ts](../../src/lib/stores/index.ts)

```typescript
// Authentication & Config
export const user = writable<SessionUser | undefined>(undefined);
export const config = writable<Config | undefined>(undefined);
export const settings = writable<Settings>({});

// LLM & AI
export const models = writable<Model[]>([]);
export const prompts = writable<Prompt[] | null>(null);
export const tools = writable(null);
export const functions = writable(null);
export const knowledge = writable<Document[] | null>(null);

// Chats & Messaging
export const chats = writable(null);
export const channels = writable([]);
export const tags = writable([]);

// UI State
export const showSidebar = writable(false);
export const showSettings = writable(false);
export const showSearch = writable(false);
export const mobile = writable(false);

// Real-time
export const socket = writable<Socket | null>(null);
export const activeUserIds = writable<string[] | null>(null);
```

### Local State vs Global State

**Use local `$state()` when:**
- State is component-specific
- Doesn't need to be shared
- Short-lived (unmounts with component)

**Use global stores when:**
- State needs to be shared across components
- Persists across navigation
- Needs to be accessed from API clients

---

## Component Architecture

### Component Organization

```
src/lib/components/
├── chat/                  # Feature-based folder
│   ├── Chat.svelte       # Main component
│   ├── Messages/
│   │   ├── Message.svelte
│   │   ├── UserMessage.svelte
│   │   └── AssistantMessage.svelte
│   ├── MessageInput/
│   │   ├── Input.svelte
│   │   └── Toolbar.svelte
│   └── Settings/
│       └── ChatSettings.svelte
├── common/                # Shared components
│   ├── Button.svelte
│   ├── Modal.svelte
│   └── Spinner.svelte
└── layout/                # Layout components
    ├── Sidebar.svelte
    └── Navbar.svelte
```

### Component Patterns

#### 1. Container/Presentational Pattern

**Container (Smart):**
```svelte
<!-- ChatContainer.svelte -->
<script lang="ts">
  import { onMount } from 'svelte';
  import { getMessages } from '$lib/apis/chats';
  import MessageList from './MessageList.svelte';

  let messages = $state([]);
  let loading = $state(true);

  onMount(async () => {
    messages = await getMessages();
    loading = false;
  });
</script>

<MessageList {messages} {loading} />
```

**Presentational (Dumb):**
```svelte
<!-- MessageList.svelte -->
<script lang="ts">
  interface Props {
    messages: Message[];
    loading: boolean;
  }

  let { messages, loading }: Props = $props();
</script>

{#if loading}
  <Spinner />
{:else}
  {#each messages as message}
    <Message {message} />
  {/each}
{/if}
```

#### 2. Composition Pattern

```svelte
<!-- Card.svelte -->
<script lang="ts">
  import type { Snippet } from 'svelte';

  interface Props {
    title?: string;
    children: Snippet;
    footer?: Snippet;
  }

  let { title, children, footer }: Props = $props();
</script>

<div class="card">
  {#if title}
    <h3>{title}</h3>
  {/if}

  <div class="content">
    {@render children()}
  </div>

  {#if footer}
    <div class="footer">
      {@render footer()}
    </div>
  {/if}
</div>
```

**Usage:**
```svelte
<Card title="User Profile">
  <p>Content goes here</p>

  {#snippet footer()}
    <button>Save</button>
  {/snippet}
</Card>
```

#### 3. Controlled Component Pattern

```svelte
<!-- ControlledInput.svelte -->
<script lang="ts">
  interface Props {
    value: string;
    oninput: (value: string) => void;
  }

  let { value, oninput }: Props = $props();
</script>

<input
  type="text"
  value={value}
  oninput={(e) => oninput(e.currentTarget.value)}
/>
```

**Usage:**
```svelte
<script lang="ts">
  let searchQuery = $state('');
</script>

<ControlledInput
  value={searchQuery}
  oninput={(v) => searchQuery = v}
/>
```

---

## API Integration

### API Client Layer

**Location:** [src/lib/apis/](../../src/lib/apis/)

#### Structure

```
src/lib/apis/
├── index.ts              # Common utilities
├── chats/
│   └── index.ts         # Chat API functions
├── auths/
│   └── index.ts         # Auth API functions
└── ...
```

#### Example: Chat API

**File:** [src/lib/apis/chats/index.ts](../../src/lib/apis/chats/)

```typescript
const BASE_URL = '/api/v1';

export const getChats = async (token: string) => {
  const res = await fetch(`${BASE_URL}/chats/`, {
    headers: {
      Authorization: `Bearer ${token}`
    }
  });

  if (!res.ok) {
    const error = await res.json();
    throw new Error(error.detail);
  }

  return res.json();
};

export const createChat = async (token: string, chat: NewChat) => {
  const res = await fetch(`${BASE_URL}/chats/new`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      Authorization: `Bearer ${token}`
    },
    body: JSON.stringify(chat)
  });

  return res.json();
};

export const deleteChat = async (token: string, id: string) => {
  const res = await fetch(`${BASE_URL}/chats/${id}`, {
    method: 'DELETE',
    headers: {
      Authorization: `Bearer ${token}`
    }
  });

  return res.ok;
};
```

#### Using in Components

```svelte
<script lang="ts">
  import { getChats, deleteChat } from '$lib/apis/chats';
  import { user } from '$lib/stores';
  import { onMount } from 'svelte';

  let chats = $state([]);
  let loading = $state(true);
  let error = $state<string | null>(null);

  onMount(async () => {
    try {
      chats = await getChats(localStorage.token);
    } catch (err) {
      error = err.message;
    } finally {
      loading = false;
    }
  });

  async function handleDelete(id: string) {
    await deleteChat(localStorage.token, id);
    chats = chats.filter(c => c.id !== id);
  }
</script>

{#if loading}
  <Spinner />
{:else if error}
  <Error message={error} />
{:else}
  {#each chats as chat}
    <ChatItem {chat} ondelete={() => handleDelete(chat.id)} />
  {/each}
{/if}
```

---

## Real-time Features

### WebSocket Integration

```typescript
// src/lib/stores/socket.ts
import { io, type Socket } from 'socket.io-client';
import { writable } from 'svelte/store';

export const socket = writable<Socket | null>(null);

export function connectSocket(token: string) {
  const socketConnection = io('http://localhost:8080', {
    auth: { token }
  });

  socketConnection.on('connect', () => {
    console.log('Connected to WebSocket');
  });

  socketConnection.on('disconnect', () => {
    console.log('Disconnected from WebSocket');
  });

  socket.set(socketConnection);

  return socketConnection;
}
```

#### Using WebSocket in Components

```svelte
<script lang="ts">
  import { onMount, onDestroy } from 'svelte';
  import { socket } from '$lib/stores/socket';

  let messages = $state([]);

  onMount(() => {
    if ($socket) {
      $socket.on('message', (msg) => {
        messages = [...messages, msg];
      });
    }
  });

  onDestroy(() => {
    if ($socket) {
      $socket.off('message');
    }
  });

  function sendMessage(text: string) {
    $socket?.emit('message', { text });
  }
</script>
```

---

## Special Features

### Pyodide Integration

**Location:** [src/lib/pyodide/](../../src/lib/pyodide/)

Python execution in the browser:

```typescript
import { loadPyodide } from 'pyodide';

let pyodide = null;

export async function initPyodide() {
  if (!pyodide) {
    pyodide = await loadPyodide();
  }
  return pyodide;
}

export async function runPython(code: string) {
  const py = await initPyodide();
  return py.runPython(code);
}
```

### TipTap Rich Text Editor

```svelte
<script lang="ts">
  import { Editor } from '@tiptap/core';
  import StarterKit from '@tiptap/starter-kit';
  import { onMount, onDestroy } from 'svelte';

  let editorElement: HTMLElement;
  let editor: Editor;

  onMount(() => {
    editor = new Editor({
      element: editorElement,
      extensions: [StarterKit],
      content: '<p>Hello World!</p>',
      onUpdate: ({ editor }) => {
        const html = editor.getHTML();
        console.log('Content:', html);
      }
    });
  });

  onDestroy(() => {
    editor?.destroy();
  });
</script>

<div bind:this={editorElement}></div>
```

---

## Performance Optimization

### 1. Lazy Loading Components

```svelte
<script lang="ts">
  let showHeavyComponent = $state(false);
</script>

<button onclick={() => showHeavyComponent = true}>
  Load Component
</button>

{#if showHeavyComponent}
  {#await import('./HeavyComponent.svelte')}
    <Spinner />
  {:then module}
    <module.default />
  {/await}
{/if}
```

### 2. Virtual Lists

For long lists, use virtual scrolling:

```svelte
<script lang="ts">
  import VirtualList from '@sveltejs/svelte-virtual-list';

  let items = Array.from({ length: 10000 }, (_, i) => `Item ${i}`);
</script>

<VirtualList items={items} let:item>
  <div>{item}</div>
</VirtualList>
```

### 3. Memoization with $derived

```svelte
<script lang="ts">
  let items = $state([...]);

  // Expensive computation - only runs when items changes
  let processedItems = $derived(
    items.map(item => expensiveProcess(item))
  );
</script>
```

---

## Best Practices

### 1. Type Everything

```typescript
// ✅ GOOD
interface Message {
  id: string;
  content: string;
  role: 'user' | 'assistant';
}

let messages = $state<Message[]>([]);

// ❌ BAD
let messages = $state([]);
```

### 2. Extract Reusable Logic

```typescript
// composables/useMessages.ts
export function useMessages(chatId: string) {
  let messages = $state<Message[]>([]);
  let loading = $state(true);

  async function load() {
    loading = true;
    messages = await getMessages(chatId);
    loading = false;
  }

  return { messages, loading, load };
}
```

### 3. Handle Errors Gracefully

```svelte
<script lang="ts">
  let error = $state<string | null>(null);

  async function fetchData() {
    try {
      const data = await api.getData();
      return data;
    } catch (err) {
      error = err instanceof Error ? err.message : 'Unknown error';
      return null;
    }
  }
</script>

{#if error}
  <ErrorBanner message={error} />
{/if}
```

---

## Next Steps

1. **Backend patterns:** [BACKEND_ARCHITECTURE.md](./BACKEND_ARCHITECTURE.md)
2. **Database:** [DATABASE_ARCHITECTURE.md](./DATABASE_ARCHITECTURE.md)
3. **Build features:** [HOW_TO_GUIDE.md](./HOW_TO_GUIDE.md)

---

**Last updated:** November 18, 2025
**Tech stack research:** [TECH_STACK_RESEARCH.md](./TECH_STACK_RESEARCH.md)
