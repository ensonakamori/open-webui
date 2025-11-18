# Database Architecture

**Complete guide to Open WebUI's database schema, models, and query patterns.**

**Estimated reading time:** 1 hour
**Prerequisites:** [BACKEND_ARCHITECTURE.md](./BACKEND_ARCHITECTURE.md)
**Documented:** November 18, 2025

---

## Overview

### Database Options

**Default:** SQLite (file-based, simple)
**Production:** PostgreSQL (scalable, advanced features)

### Schema Overview

```
Database: open_webui
├── user               # User accounts
├── auth               # Auth tokens/sessions
├── chat               # Chat conversations
├── message            # Individual messages
├── file               # File uploads
├── folder             # Organization folders
├── knowledge          # Knowledge bases (RAG)
├── document           # Documents for RAG
├── model              # LLM model configs
├── prompt             # Prompt templates
├── tool               # Custom tools
├── function           # Custom functions
├── channel            # Collaboration channels
├── group              # User groups
├── memory             # Conversation memory
└── ... (15+ more tables)
```

---

## Core Tables

### Users Table

```python
class User(Base):
    __tablename__ = "user"

    id: Mapped[str] = mapped_column(String, primary_key=True)
    name: Mapped[str] = mapped_column(String)
    email: Mapped[str] = mapped_column(String, unique=True)
    role: Mapped[str] = mapped_column(String)  # 'admin' | 'user' | 'pending'
    password: Mapped[str] = mapped_column(String)  # Hashed
    profile_image_url: Mapped[str] = mapped_column(Text)
    created_at: Mapped[int] = mapped_column(BigInteger)
    updated_at: Mapped[int] = mapped_column(BigInteger)
```

**Indexes:**
- PRIMARY KEY: `id`
- UNIQUE: `email`

### Chats Table

```python
class Chat(Base):
    __tablename__ = "chat"

    id: Mapped[str] = mapped_column(String, primary_key=True)
    user_id: Mapped[str] = mapped_column(String, ForeignKey("user.id"))
    title: Mapped[str] = mapped_column(Text)
    chat: Mapped[dict] = mapped_column(JSON)  # Full conversation data
    created_at: Mapped[int] = mapped_column(BigInteger)
    updated_at: Mapped[int] = mapped_column(BigInteger)
    share_id: Mapped[Optional[str]] = mapped_column(String, nullable=True)
    archived: Mapped[bool] = mapped_column(Boolean, default=False)
```

**Indexes:**
- PRIMARY KEY: `id`
- INDEX: `user_id` (for fast user lookups)
- INDEX: `updated_at` (for sorting)

### Files Table

```python
class File(Base):
    __tablename__ = "file"

    id: Mapped[str] = mapped_column(String, primary_key=True)
    user_id: Mapped[str] = mapped_column(String)
    filename: Mapped[str] = mapped_column(Text)
    path: Mapped[str] = mapped_column(Text)
    meta: Mapped[dict] = mapped_column(JSON)
    created_at: Mapped[int] = mapped_column(BigInteger)
```

---

## Relationships

### One-to-Many: User → Chats

```python
class User(Base):
    chats: Mapped[list["Chat"]] = relationship(back_populates="user")

class Chat(Base):
    user: Mapped["User"] = relationship(back_populates="chats")

# Usage
user = Users.get_user_by_id(user_id)
for chat in user.chats:  # Lazy loaded
    print(chat.title)

# Eager load with select
stmt = select(User).options(selectinload(User.chats))
user = db.scalar(stmt)  # Chats already loaded
```

---

## Migrations

### Alembic Setup

**Location:** `backend/open_webui/migrations/`

### Create Migration

```bash
cd backend
alembic revision -m "Add column to users table"
```

**Generated file:** `migrations/versions/xxxxx_add_column_to_users_table.py`

```python
from alembic import op
import sqlalchemy as sa

def upgrade():
    op.add_column('user', sa.Column('bio', sa.Text(), nullable=True))

def downgrade():
    op.drop_column('user', 'bio')
```

### Apply Migrations

```bash
# From project root

# Upgrade to latest
alembic -c backend/open_webui/alembic.ini upgrade head

# Downgrade one version
alembic -c backend/open_webui/alembic.ini downgrade -1

# Show current version
alembic -c backend/open_webui/alembic.ini current
```

---

## Query Patterns

### Select Queries

```python
# Get single record
stmt = select(User).where(User.id == user_id)
user = db.scalar(stmt)

# Get all records
stmt = select(Chat).where(Chat.user_id == user_id)
chats = db.scalars(stmt).all()

# With ordering
stmt = (
    select(Chat)
    .where(Chat.user_id == user_id)
    .order_by(Chat.updated_at.desc())
)

# With pagination
stmt = stmt.limit(20).offset(40)

# With joins
stmt = (
    select(Chat, User)
    .join(User, Chat.user_id == User.id)
    .where(User.role == "admin")
)
```

### Insert

```python
new_chat = Chat(
    id=str(uuid4()),
    user_id=user_id,
    title="New Chat",
    chat={},
    created_at=int(time.time()),
    updated_at=int(time.time())
)

db.add(new_chat)
db.commit()
db.refresh(new_chat)  # Get updated fields
```

### Update

```python
stmt = (
    update(Chat)
    .where(Chat.id == chat_id)
    .values(title="Updated Title", updated_at=int(time.time()))
)
db.execute(stmt)
db.commit()
```

### Delete

```python
stmt = delete(Chat).where(Chat.id == chat_id)
db.execute(stmt)
db.commit()
```

---

## Vector Database (ChromaDB)

### Schema

ChromaDB stores:
- **Embeddings** - Vector representations (768 or 1536 dims)
- **Documents** - Original text chunks
- **Metadata** - file_id, chunk_index, etc.

### Operations

```python
import chromadb

client = chromadb.Client()

# Create collection
collection = client.create_collection("documents")

# Add documents
collection.add(
    ids=["id1", "id2"],
    embeddings=[[0.1, 0.2, ...], [0.3, 0.4, ...]],
    documents=["chunk 1 text", "chunk 2 text"],
    metadatas=[{"file_id": "abc"}, {"file_id": "abc"}]
)

# Search
results = collection.query(
    query_embeddings=[[0.1, 0.2, ...]],
    n_results=5
)
```

---

## Best Practices

### 1. Use Transactions

```python
with get_db() as db:
    try:
        # Multiple operations
        db.add(user)
        db.add(chat)
        db.commit()
    except Exception:
        db.rollback()
        raise
```

### 2. Avoid N+1 Queries

```python
# ❌ BAD - N+1 queries
users = db.scalars(select(User)).all()
for user in users:
    print(user.chats)  # Separate query for each user!

# ✅ GOOD - Eager load
stmt = select(User).options(selectinload(User.chats))
users = db.scalars(stmt).all()
for user in users:
    print(user.chats)  # Already loaded!
```

### 3. Index Frequently Queried Columns

```python
# Add index in migration
op.create_index('idx_chat_user_id', 'chat', ['user_id'])
op.create_index('idx_chat_updated_at', 'chat', ['updated_at'])
```

---

**Last updated:** November 18, 2025
