# Patterns and Conventions

**Code style, naming conventions, and best practices for Open WebUI development.**

**Documented:** November 18, 2025

---

## Code Style

### Frontend (TypeScript/Svelte)

**Formatting:** Prettier
**Linting:** ESLint

```bash
# Format code
npm run format

# Lint
npm run lint:frontend
```

### Backend (Python)

**Formatting:** Black
**Linting:** Pylint

```bash
# Format code
npm run format:backend

# Lint
npm run lint:backend
```

---

## Naming Conventions

### Frontend

**Files:**
- Components: `PascalCase.svelte`
- Utilities: `camelCase.ts`
- Routes: `+page.svelte`, `+layout.svelte`

**Variables:**
```typescript
const userName = 'John';        // camelCase
const MAX_RETRIES = 3;          // UPPER_SNAKE_CASE for constants
interface UserData {}           // PascalCase for types
```

###Backend

**Files:** `snake_case.py`

**Variables:**
```python
user_name = 'John'              # snake_case
MAX_RETRIES = 3                 # UPPER_SNAKE_CASE for constants
class UserModel:                 # PascalCase for classes
```

---

## Component Patterns

### Svelte 5 Patterns

```svelte
<!-- ✅ GOOD -->
<script lang="ts">
  let count = $state(0);
  let doubled = $derived(count * 2);

  $effect(() => {
    console.log('Count changed');
  });
</script>

<!-- ❌ AVOID - Old Svelte 3/4 -->
<script>
  let count = 0;
  $: doubled = count * 2;
</script>
```

---

## API Client Patterns

**File structure:**
```
src/lib/apis/
└── chats/
    └── index.ts
```

**Pattern:**
```typescript
export const getChats = async (token: string) => {
  const res = await fetch('/api/v1/chats/', {
    headers: { Authorization: `Bearer ${token}` }
  });
  
  if (!res.ok) throw new Error(await res.text());
  return res.json();
};
```

---

## Router Patterns

**File:** One router per domain

```python
from fastapi import APIRouter, Depends
from open_webui.utils.auth import get_verified_user

router = APIRouter()

@router.get("/")
async def get_items(user=Depends(get_verified_user)):
    return Items.get_by_user_id(user.id)
```

---

## Database Patterns

**✅ SQLAlchemy 2.0:**
```python
stmt = select(User).where(User.id == user_id)
user = db.scalar(stmt)
```

**❌ Deprecated 1.x:**
```python
user = db.query(User).filter(User.id == user_id).first()
```

---

## Error Handling

**Frontend:**
```typescript
try {
  const data = await api.call();
} catch (error) {
  toast.error(error.message);
}
```

**Backend:**
```python
from fastapi import HTTPException, status

if not resource:
    raise HTTPException(
        status_code=status.HTTP_404_NOT_FOUND,
        detail="Resource not found"
    )
```

---

**Last updated:** November 18, 2025
