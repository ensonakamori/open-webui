# Development Workflow

**Git workflow, testing, and deployment process.**

**Documented:** November 18, 2025

---

## Git Workflow

### Branch Strategy

- `main` - Production-ready code
- `dev` - Development branch
- `feature/*` - Feature branches
- `fix/*` - Bug fix branches

### Creating a Feature

```bash
# Create branch
git checkout -b feature/my-feature

# Make changes
git add .
git commit -m "Add my feature"

# Push
git push origin feature/my-feature

# Create pull request on GitHub
```

---

## Testing

### Frontend Tests

```bash
# Run Vitest
npm run test:frontend

# E2E tests
npm run cy:open
```

### Backend Tests

```bash
# Run pytest
cd backend
pytest
```

---

## Code Review

1. Create PR
2. Request review
3. Address feedback
4. Get approval
5. Merge to main

---

## Deployment

### Docker

```bash
docker build -t open-webui .
docker run -p 3000:8080 open-webui
```

### Kubernetes

```bash
kubectl apply -k ./kubernetes/manifest
```

---

**Last updated:** November 18, 2025
