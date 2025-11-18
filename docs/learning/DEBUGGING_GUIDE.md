# Debugging Guide

**Debugging techniques and common issues.**

**Documented:** November 18, 2025

---

## Frontend Debugging

### Browser DevTools

1. Open DevTools (F12)
2. Set breakpoints in Sources tab
3. Inspect network requests in Network tab
4. Check console for errors

### Svelte DevTools

Install: [Svelte DevTools Extension](https://chrome.google.com/webstore)

---

## Backend Debugging

### VS Code Debugger

`.vscode/launch.json`:
```json
{
  "configurations": [
    {
      "name": "FastAPI",
      "type": "python",
      "request": "launch",
      "module": "uvicorn",
      "args": ["open_webui.main:app", "--reload"],
      "cwd": "${workspaceFolder}/backend"
    }
  ]
}
```

### Python Debugger

```python
import pdb; pdb.set_trace()  # Breakpoint
```

---

## Common Issues

### "Cannot connect to backend"
- Check backend is running on port 8080
- Verify proxy settings in `vite.config.ts`

### "Database locked" (SQLite)
- Close other connections
- Switch to PostgreSQL for production

### "Module not found"
- Frontend: `rm -rf node_modules && npm install`
- Backend: `pip install -e .`

---

**Last updated:** November 18, 2025
