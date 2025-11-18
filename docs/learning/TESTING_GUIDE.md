# Testing Guide

**How to write and run tests for Open WebUI.**

**Documented:** November 18, 2025

---

## Frontend Testing (Vitest)

### Unit Tests

```typescript
// src/lib/utils/format.test.ts
import { describe, it, expect } from 'vitest';
import { formatDate } from './format';

describe('formatDate', () => {
  it('formats timestamp correctly', () => {
    const result = formatDate(1700000000);
    expect(result).toBe('Nov 14, 2023');
  });
});
```

**Run:**
```bash
npm run test:frontend
```

---

## E2E Testing (Cypress)

```typescript
// cypress/e2e/chat.cy.ts
describe('Chat Flow', () => {
  it('sends message successfully', () => {
    cy.visit('/');
    cy.get('[data-testid="message-input"]').type('Hello{enter}');
    cy.contains('Hello').should('be.visible');
  });
});
```

**Run:**
```bash
npm run cy:open
```

---

## Backend Testing (Pytest)

```python
# backend/tests/test_chats.py
def test_create_chat(client, auth_headers):
    response = client.post(
        "/api/v1/chats/new",
        json={"title": "Test Chat"},
        headers=auth_headers
    )
    
    assert response.status_code == 200
    assert response.json()["title"] == "Test Chat"
```

**Run:**
```bash
cd backend
pytest
```

---

**Last updated:** November 18, 2025
