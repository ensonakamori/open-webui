# Security Guide

**Security best practices for Open WebUI.**

**Documented:** November 18, 2025

---

## Authentication

### JWT Tokens
- Stored in httpOnly cookies (not localStorage)
- 7-day expiration
- Signed with HS256

### Password Hashing
- bcrypt with salt rounds
- Never store plain passwords

---

## Authorization

### Role-Based Access Control (RBAC)

**Roles:**
- `admin` - Full access
- `user` - Standard access
- `pending` - Awaiting approval

### Permissions Check

```python
from open_webui.utils.access_control import has_permission

if not has_permission(user.id, "chat.delete", permissions):
    raise HTTPException(status_code=403)
```

---

## Input Validation

### Frontend
```typescript
// Sanitize user input
import DOMPurify from 'dompurify';

const clean = DOMPurify.sanitize(userInput);
```

### Backend
```python
# Pydantic validation
class UserInput(BaseModel):
    email: EmailStr  # Validates email
    age: int = Field(ge=0, le=150)  # Range validation
```

---

## API Security

### Rate Limiting
```python
from slowapi import Limiter

limiter = Limiter(key_func=get_remote_address)

@app.get("/")
@limiter.limit("100/minute")
async def endpoint():
    pass
```

### CORS
```python
app.add_middleware(
    CORSMiddleware,
    allow_origins=["https://yourdomain.com"],  # Not "*" in production!
    allow_credentials=True
)
```

---

## Database Security

- Use parameterized queries (SQLAlchemy does this)
- Never concatenate SQL strings
- Encrypt sensitive data at rest

---

**Last updated:** November 18, 2025
