# How-To Guide

**Step-by-step guides for common development tasks.**

**Documented:** November 18, 2025

---

## How to Add a New Page

1. Create route file:
   ```svelte
   <!-- src/routes/(app)/my-page/+page.svelte -->
   <script lang="ts">
     let title = $state('My Page');
   </script>

   <h1>{title}</h1>
   ```

2. Navigate: `/my-page`

---

## How to Add an API Endpoint

1. Create in router:
   ```python
   # backend/open_webui/routers/my_router.py
   from fastapi import APIRouter

   router = APIRouter()

   @router.get("/items")
   async def get_items():
       return [{"id": 1, "name": "Item"}]
   ```

2. Register in `main.py`:
   ```python
   from open_webui.routers import my_router
   app.include_router(my_router.router, prefix="/api/v1/my", tags=["my"])
   ```

---

## How to Add a Database Model

1. Create model:
   ```python
   # backend/open_webui/models/items.py
   from sqlalchemy.orm import Mapped, mapped_column
   from open_webui.internal.db import Base

   class Item(Base):
       __tablename__ = "item"

       id: Mapped[str] = mapped_column(String, primary_key=True)
       name: Mapped[str] = mapped_column(String)
   ```

2. Create migration:
   ```bash
   cd backend
   alembic revision -m "Add items table"
   ```

3. Apply:
   ```bash
   alembic upgrade head
   ```

---

## How to Add Global State

1. Add to stores:
   ```typescript
   // src/lib/stores/index.ts
   export const myData = writable<Data[]>([]);
   ```

2. Use in components:
   ```svelte
   <script>
     import { myData } from '$lib/stores';
   </script>

   {#each $myData as item}
     <div>{item.name}</div>
   {/each}
   ```

---

## How to Add a Custom Function

1. Create in Admin Panel → Workspace → Functions
2. Write Python code
3. Enable for models
4. Use in chat with function calling

---

**Last updated:** November 18, 2025
