# Database Schema

**Complete database schema reference.**

**Documented:** November 18, 2025

---

## Core Tables

### user

| Column | Type | Constraints |
|--------|------|-------------|
| id | VARCHAR | PRIMARY KEY |
| name | VARCHAR | NOT NULL |
| email | VARCHAR | UNIQUE, NOT NULL |
| password | VARCHAR | NOT NULL (hashed) |
| role | VARCHAR | NOT NULL |
| profile_image_url | TEXT | |
| created_at | BIGINT | NOT NULL |
| updated_at | BIGINT | NOT NULL |

---

### chat

| Column | Type | Constraints |
|--------|------|-------------|
| id | VARCHAR | PRIMARY KEY |
| user_id | VARCHAR | FOREIGN KEY → user(id) |
| title | TEXT | NOT NULL |
| chat | JSON | NOT NULL |
| created_at | BIGINT | NOT NULL |
| updated_at | BIGINT | NOT NULL |
| share_id | VARCHAR | NULLABLE |
| archived | BOOLEAN | DEFAULT FALSE |

**Indexes:**
- idx_chat_user_id (user_id)
- idx_chat_updated_at (updated_at)

---

### file

| Column | Type | Constraints |
|--------|------|-------------|
| id | VARCHAR | PRIMARY KEY |
| user_id | VARCHAR | FOREIGN KEY → user(id) |
| filename | TEXT | NOT NULL |
| path | TEXT | NOT NULL |
| meta | JSON | |
| created_at | BIGINT | NOT NULL |

---

### knowledge

| Column | Type | Constraints |
|--------|------|-------------|
| id | VARCHAR | PRIMARY KEY |
| user_id | VARCHAR | FOREIGN KEY → user(id) |
| name | TEXT | NOT NULL |
| description | TEXT | |
| data | JSON | |
| created_at | BIGINT | NOT NULL |
| updated_at | BIGINT | NOT NULL |

---

## ER Diagram

```
user (1) ────< (N) chat
user (1) ────< (N) file
user (1) ────< (N) knowledge
```

---

**Last updated:** November 18, 2025
