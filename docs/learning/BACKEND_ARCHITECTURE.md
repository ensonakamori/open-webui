# Backend Architecture

**Deep dive into Open WebUI's Python backend: FastAPI, SQLAlchemy 2.0, async patterns, and LLM integrations.**

**Estimated reading time:** 2-3 hours
**Prerequisites:** [TECH_STACK_GUIDE.md](./TECH_STACK_GUIDE.md), [PROJECT_STRUCTURE.md](./PROJECT_STRUCTURE.md)
**Documented:** November 18, 2025

---

## 📋 Table of Contents

1. [Overview](#overview)
2. [FastAPI Fundamentals](#fastapi-fundamentals)
3. [Router Organization](#router-organization)
4. [SQLAlchemy 2.0 Patterns](#sqlalchemy-20-patterns)
5. [Authentication & Authorization](#authentication--authorization)
6. [LLM Integration](#llm-integration)
7. [Background Tasks](#background-tasks)
8. [WebSocket Implementation](#websocket-implementation)
9. [Best Practices](#best-practices)

---

## Overview

### Backend Tech Stack

```
┌────────────────────────────────────┐
│     Python 3.11-3.12               │  Language
├────────────────────────────────────┤
│     FastAPI 0.118                  │  Web framework
├────────────────────────────────────┤
│     SQLAlchemy 2.0                 │  ORM
├────────────────────────────────────┤
│     Pydantic v2                    │  Validation
├────────────────────────────────────┤
│     Uvicorn                        │  ASGI server
└────────────────────────────────────┘
```

### Directory Structure Recap

```
backend/
└── open_webui/
    ├── routers/           # API endpoints (25+ routers)
    ├── models/            # Database models
    ├── socket/            # WebSocket handlers
    ├── utils/             # Utilities
    ├── config.py          # Configuration
    └── main.py            # FastAPI app
```

---

## FastAPI Fundamentals

### Application Setup

**File:** [backend/open_webui/main.py](../../backend/open_webui/main.py)

```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware

app = FastAPI(
    title="Open WebUI API",
    version="0.1.0"
)

# CORS middleware
app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],  # Configure properly in production
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

# Include routers
from open_webui.routers import (
    auths, chats, models, ollama,
    openai, retrieval, # ... and 20+ more
)

app.include_router(auths.router, prefix="/api/v1/auths", tags=["auths"])
app.include_router(chats.router, prefix="/api/v1/chats", tags=["chats"])
# ... more routers
```

### 🌉 For Express.js Developers

| Express.js | FastAPI |
|------------|---------|
| `app.get('/users')` | `@app.get("/users")` |
| `req.params.id` | Function parameter with type |
| `req.body` | Pydantic model |
| Middleware | Depends() or middleware |
| `res.json(...)` | Return Python dict |

---

## Router Organization

### Router Pattern

Each router handles a specific domain (chats, users, files, etc.)

**File:** [backend/open_webui/routers/chats.py](../../backend/open_webui/routers/chats.py)

```python
from fastapi import APIRouter, Depends, HTTPException, status
from pydantic import BaseModel
from open_webui.models.chats import Chats
from open_webui.utils.auth import get_verified_user

router = APIRouter()

# Pydantic model for request validation
class ChatForm(BaseModel):
    title: str
    chat: dict

# GET endpoint
@router.get("/", response_model=list[ChatTitleIdResponse])
def get_session_user_chat_list(
    user=Depends(get_verified_user),  # Dependency injection!
    page: Optional[int] = None,
):
    if page is not None:
        skip = (page - 1) * 60
        return Chats.get_chat_title_id_list_by_user_id(
            user.id,
            skip=skip,
            limit=60
        )
    return Chats.get_chat_title_id_list_by_user_id(user.id)

# POST endpoint
@router.post("/new", response_model=ChatResponse)
async def create_new_chat(
    form_data: ChatForm,
    user=Depends(get_verified_user)
):
    chat = Chats.insert_new_chat(user.id, form_data)
    return ChatResponse(**chat.model_dump())

# DELETE endpoint
@router.delete("/{id}")
async def delete_chat_by_id(
    id: str,
    user=Depends(get_verified_user)
):
    chat = Chats.get_chat_by_id(id)

    if not chat:
        raise HTTPException(
            status_code=status.HTTP_404_NOT_FOUND,
            detail="Chat not found"
        )

    if chat.user_id != user.id:
        raise HTTPException(
            status_code=status.HTTP_403_FORBIDDEN,
            detail="Access denied"
        )

    result = Chats.delete_chat_by_id(id)
    return {"success": result}
```

### 🎯 Key Patterns

1. **Type hints everywhere** - FastAPI uses them for validation
2. **Depends() for DI** - Inject dependencies (auth, db, etc.)
3. **Pydantic models** - Automatic request/response validation
4. **response_model** - Type-safe responses
5. **HTTPException** - Standard error handling

---

## SQLAlchemy 2.0 Patterns

### ✅ CURRENT: SQLAlchemy 2.0

**File:** [backend/open_webui/models/chats.py](../../backend/open_webui/models/chats.py)

```python
from sqlalchemy import String, Column, BigInteger, Text, JSON
from sqlalchemy.orm import Mapped, mapped_column
from open_webui.internal.db import Base, get_db

# Model definition (NEW 2.0 style)
class Chat(Base):
    __tablename__ = "chat"

    id: Mapped[str] = mapped_column(String, primary_key=True)
    user_id: Mapped[str] = mapped_column(String)
    title: Mapped[str] = mapped_column(Text)
    chat: Mapped[dict] = mapped_column(JSON)
    created_at: Mapped[int] = mapped_column(BigInteger)
    updated_at: Mapped[int] = mapped_column(BigInteger)
```

### Database Operations

**Class-based pattern:**

```python
from sqlalchemy import select, delete, update
from open_webui.internal.db import get_db

class Chats:
    @staticmethod
    def get_chat_by_id(id: str) -> Optional[Chat]:
        with get_db() as db:
            stmt = select(Chat).where(Chat.id == id)
            return db.scalar(stmt)

    @staticmethod
    def get_chat_title_id_list_by_user_id(
        user_id: str,
        skip: int = 0,
        limit: Optional[int] = None
    ) -> list[Chat]:
        with get_db() as db:
            stmt = (
                select(Chat)
                .where(Chat.user_id == user_id)
                .order_by(Chat.updated_at.desc())
                .offset(skip)
            )

            if limit:
                stmt = stmt.limit(limit)

            return db.scalars(stmt).all()

    @staticmethod
    def insert_new_chat(user_id: str, form_data: ChatForm) -> Chat:
        with get_db() as db:
            chat = Chat(
                id=str(uuid4()),
                user_id=user_id,
                title=form_data.title,
                chat=form_data.chat,
                created_at=int(time.time()),
                updated_at=int(time.time())
            )

            db.add(chat)
            db.commit()
            db.refresh(chat)

            return chat

    @staticmethod
    def delete_chat_by_id(id: str) -> bool:
        with get_db() as db:
            stmt = delete(Chat).where(Chat.id == id)
            result = db.execute(stmt)
            db.commit()

            return result.rowcount > 0
```

### 💡 Aha Moment

**SQLAlchemy 2.0 uses `select()` statements**:

```python
# ❌ OLD 1.x style - DON'T USE
chats = session.query(Chat).filter(Chat.user_id == user_id).all()

# ✅ NEW 2.0 style - USE THIS
stmt = select(Chat).where(Chat.user_id == user_id)
chats = session.scalars(stmt).all()
```

### Relationships

```python
from sqlalchemy.orm import relationship, Mapped

class User(Base):
    __tablename__ = "user"

    id: Mapped[str] = mapped_column(String, primary_key=True)
    name: Mapped[str] = mapped_column(String)

    # Relationship (one-to-many)
    chats: Mapped[list["Chat"]] = relationship(back_populates="user")

class Chat(Base):
    __tablename__ = "chat"

    id: Mapped[str] = mapped_column(String, primary_key=True)
    user_id: Mapped[str] = mapped_column(String, ForeignKey("user.id"))

    # Relationship (many-to-one)
    user: Mapped["User"] = relationship(back_populates="chats")
```

**Eager loading:**

```python
from sqlalchemy.orm import selectinload

# Load user with all their chats
stmt = (
    select(User)
    .options(selectinload(User.chats))
    .where(User.id == user_id)
)
user = db.scalar(stmt)
# user.chats is already loaded (no N+1 query!)
```

---

## Authentication & Authorization

### JWT Authentication

**File:** [backend/open_webui/utils/auth.py](../../backend/open_webui/utils/)

```python
from fastapi import Depends, HTTPException, status
from fastapi.security import HTTPBearer, HTTPAuthorizationCredentials
from jose import jwt, JWTError
from datetime import datetime, timedelta

security = HTTPBearer()

def create_token(data: dict) -> str:
    to_encode = data.copy()
    expire = datetime.utcnow() + timedelta(days=7)
    to_encode.update({"exp": expire})

    encoded_jwt = jwt.encode(
        to_encode,
        SECRET_KEY,
        algorithm="HS256"
    )
    return encoded_jwt

def decode_token(token: str) -> dict:
    try:
        payload = jwt.decode(
            token,
            SECRET_KEY,
            algorithms=["HS256"]
        )
        return payload
    except JWTError:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Invalid token"
        )
```

### Dependency Injection for Auth

```python
from open_webui.models.users import Users

async def get_current_user(
    credentials: HTTPAuthorizationCredentials = Depends(security)
) -> User:
    token = credentials.credentials
    payload = decode_token(token)

    user_id = payload.get("id")
    if not user_id:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Invalid token"
        )

    user = Users.get_user_by_id(user_id)
    if not user:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="User not found"
        )

    return user

# Wrapper for verified users
async def get_verified_user(
    user: User = Depends(get_current_user)
) -> User:
    if not user.email_verified:
        raise HTTPException(
            status_code=status.HTTP_403_FORBIDDEN,
            detail="Email not verified"
        )
    return user

# Admin only
async def get_admin_user(
    user: User = Depends(get_verified_user)
) -> User:
    if user.role != "admin":
        raise HTTPException(
            status_code=status.HTTP_403_FORBIDDEN,
            detail="Admin access required"
        )
    return user
```

### Using in Routers

```python
@router.get("/admin-only")
async def admin_endpoint(
    user=Depends(get_admin_user)  # Only admins can access
):
    return {"message": f"Hello admin {user.name}"}

@router.get("/verified-only")
async def verified_endpoint(
    user=Depends(get_verified_user)  # Verified users only
):
    return {"message": f"Hello {user.name}"}
```

---

## LLM Integration

### LangChain Integration

⚠️ **Note:** Project uses LangChain 0.3 (deprecated, v1.0 available)

**File:** [backend/open_webui/routers/openai.py](../../backend/open_webui/routers/openai.py)

```python
from langchain.chat_models import ChatOpenAI
from langchain.schema import HumanMessage, SystemMessage

async def stream_chat_completion(
    messages: list[dict],
    model: str = "gpt-3.5-turbo"
):
    # Initialize LLM
    llm = ChatOpenAI(
        model=model,
        streaming=True,
        temperature=0.7
    )

    # Convert messages to LangChain format
    lc_messages = [
        SystemMessage(content=m["content"]) if m["role"] == "system"
        else HumanMessage(content=m["content"])
        for m in messages
    ]

    # Stream response
    async for chunk in llm.astream(lc_messages):
        yield chunk.content
```

### Streaming Responses

```python
from fastapi.responses import StreamingResponse

@router.post("/chat/completions")
async def chat_completions(
    request: ChatCompletionRequest,
    user=Depends(get_verified_user)
):
    async def generate():
        async for token in stream_chat_completion(request.messages):
            # Server-Sent Events format
            yield f"data: {json.dumps({'token': token})}\n\n"

        yield "data: [DONE]\n\n"

    return StreamingResponse(
        generate(),
        media_type="text/event-stream"
    )
```

---

## Background Tasks

### APScheduler

**File:** [backend/open_webui/main.py](../../backend/open_webui/main.py)

```python
from apscheduler.schedulers.asyncio import AsyncIOScheduler

scheduler = AsyncIOScheduler()

@app.on_event("startup")
async def startup_event():
    # Schedule periodic tasks
    scheduler.add_job(
        cleanup_old_sessions,
        "interval",
        hours=24,
        id="cleanup_sessions"
    )

    scheduler.add_job(
        update_model_list,
        "interval",
        minutes=30,
        id="update_models"
    )

    scheduler.start()

async def cleanup_old_sessions():
    """Remove expired sessions"""
    cutoff = int(time.time()) - (7 * 24 * 60 * 60)  # 7 days
    Sessions.delete_sessions_before(cutoff)

async def update_model_list():
    """Refresh available models"""
    models = await fetch_models_from_providers()
    # Update cache/database
```

### FastAPI Background Tasks

```python
from fastapi import BackgroundTasks

@router.post("/process")
async def process_file(
    file: UploadFile,
    background_tasks: BackgroundTasks,
    user=Depends(get_verified_user)
):
    # Save file immediately
    file_id = save_file(file)

    # Process in background
    background_tasks.add_task(
        process_document,
        file_id,
        user.id
    )

    return {"file_id": file_id, "status": "processing"}

async def process_document(file_id: str, user_id: str):
    """Heavy processing task"""
    # Extract text
    text = extract_text(file_id)

    # Generate embeddings
    embeddings = generate_embeddings(text)

    # Store in vector DB
    store_embeddings(file_id, embeddings)

    # Notify user
    notify_user(user_id, f"Document {file_id} processed")
```

---

## WebSocket Implementation

### Socket.io Server

**File:** [backend/open_webui/socket/main.py](../../backend/open_webui/socket/main.py)

```python
import socketio

sio = socketio.AsyncServer(
    async_mode='asgi',
    cors_allowed_origins='*'
)

@sio.event
async def connect(sid, environ, auth):
    """Handle new connection"""
    token = auth.get('token')

    # Verify JWT
    try:
        payload = decode_token(token)
        user_id = payload['id']
    except:
        raise ConnectionRefusedError('Invalid token')

    # Store user session
    await sio.save_session(sid, {'user_id': user_id})

    print(f"User {user_id} connected: {sid}")

@sio.event
async def disconnect(sid):
    """Handle disconnection"""
    session = await sio.get_session(sid)
    print(f"User {session['user_id']} disconnected")

@sio.event
async def join_channel(sid, data):
    """Join a channel/room"""
    channel_id = data['channel_id']
    session = await sio.get_session(sid)

    # Join Socket.io room
    sio.enter_room(sid, channel_id)

    # Notify others
    await sio.emit(
        'user-joined',
        {'user_id': session['user_id']},
        room=channel_id,
        skip_sid=sid  # Don't send to self
    )

@sio.event
async def send_message(sid, data):
    """Broadcast message to channel"""
    session = await sio.get_session(sid)
    channel_id = data['channel_id']

    # Broadcast to all in room
    await sio.emit(
        'message',
        {
            'user_id': session['user_id'],
            'text': data['text'],
            'timestamp': int(time.time())
        },
        room=channel_id
    )

    # Save to database
    await save_channel_message(channel_id, session['user_id'], data['text'])
```

### Mount Socket.io in FastAPI

```python
from fastapi import FastAPI
from open_webui.socket.main import sio

app = FastAPI()

# Mount Socket.io
socket_app = socketio.ASGIApp(sio, app)

# Note: socket_app wraps FastAPI app
```

---

## Best Practices

### 1. Type Everything

```python
# ✅ GOOD
from typing import Optional

async def get_user(user_id: str) -> Optional[User]:
    return Users.get_user_by_id(user_id)

# ❌ BAD
async def get_user(user_id):
    return Users.get_user_by_id(user_id)
```

### 2. Use Pydantic Models

```python
from pydantic import BaseModel, EmailStr, validator

class UserCreate(BaseModel):
    email: EmailStr  # Validates email format
    password: str
    name: str

    @validator('password')
    def validate_password(cls, v):
        if len(v) < 8:
            raise ValueError('Password must be at least 8 characters')
        return v
```

### 3. Handle Errors Gracefully

```python
from fastapi import HTTPException, status

@router.get("/resource/{id}")
async def get_resource(id: str):
    try:
        resource = await db.get(id)
    except DatabaseError as e:
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="Database error"
        )

    if not resource:
        raise HTTPException(
            status_code=status.HTTP_404_NOT_FOUND,
            detail=f"Resource {id} not found"
        )

    return resource
```

### 4. Use Async Throughout

```python
# ✅ GOOD - Async
@router.get("/")
async def get_items():
    items = await db.query_async("SELECT * FROM items")
    return items

# ❌ AVOID - Sync (blocks event loop)
@router.get("/")
def get_items():
    items = db.query_sync("SELECT * FROM items")
    return items
```

### 5. Dependency Injection

```python
# Common dependencies
async def get_db_session():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

# Use in endpoint
@router.post("/")
async def create_item(
    item: ItemCreate,
    db: Session = Depends(get_db_session),
    user: User = Depends(get_current_user)
):
    new_item = Item(**item.dict(), owner_id=user.id)
    db.add(new_item)
    db.commit()
    return new_item
```

---

## Next Steps

1. **Database details:** [DATABASE_ARCHITECTURE.md](./DATABASE_ARCHITECTURE.md)
2. **External integrations:** [INTEGRATION_GUIDE.md](./INTEGRATION_GUIDE.md)
3. **Build features:** [HOW_TO_GUIDE.md](./HOW_TO_GUIDE.md)

---

**Last updated:** November 18, 2025
**Tech stack research:** [TECH_STACK_RESEARCH.md](./TECH_STACK_RESEARCH.md)
