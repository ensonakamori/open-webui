# API Documentation

**REST API reference for Open WebUI.**

**Documented:** November 18, 2025

---

## Base URL

```
http://localhost:8080/api/v1
```

## Authentication

All endpoints except `/auth/signin` and `/auth/signup` require authentication.

**Header:**
```
Authorization: Bearer <token>
```

---

## Endpoints

### Authentication

#### Sign In
```http
POST /auth/signin
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "password"
}

Response: {
  "token": "eyJ...",
  "user": { ... }
}
```

#### Sign Up
```http
POST /auth/signup
Content-Type: application/json

{
  "name": "John Doe",
  "email": "user@example.com",
  "password": "password"
}
```

---

### Chats

#### List Chats
```http
GET /chats/
Authorization: Bearer <token>

Response: [
  {
    "id": "chat-id",
    "title": "Chat Title",
    "created_at": 1700000000
  }
]
```

#### Create Chat
```http
POST /chats/new
Authorization: Bearer <token>
Content-Type: application/json

{
  "title": "New Chat",
  "chat": {}
}
```

#### Delete Chat
```http
DELETE /chats/{chat_id}
Authorization: Bearer <token>
```

---

### Files

#### Upload File
```http
POST /files/upload
Authorization: Bearer <token>
Content-Type: multipart/form-data

file: <binary>
```

---

## Auto-Generated Docs

Visit: http://localhost:8080/docs (Swagger UI)

---

**Last updated:** November 18, 2025
