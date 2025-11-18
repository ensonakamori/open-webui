# Integration Guide

**How to integrate Open WebUI with external services and LLM providers.**

**Documented:** November 18, 2025

---

## LLM Provider Integration

### Ollama (Local Models)

**Setup:**
```bash
# Install Ollama
curl https://ollama.ai/install.sh | sh

# Pull a model
ollama pull llama2

# Run Ollama server
ollama serve
```

**Configure in Open WebUI:**
```
Settings → Connections → Ollama
Base URL: http://localhost:11434
```

### OpenAI API

**Environment variables:**
```bash
OPENAI_API_KEY=sk-...
OPENAI_API_BASE_URL=https://api.openai.com/v1
```

**Supported models:**
- GPT-3.5-turbo
- GPT-4
- GPT-4-turbo

### Anthropic (Claude)

```bash
ANTHROPIC_API_KEY=sk-ant-...
```

### Google AI (Gemini)

```bash
GOOGLE_AI_API_KEY=...
```

---

## Vector Database Integration

### ChromaDB (Default)

No additional setup - included by default.

### Qdrant

```bash
pip install qdrant-client

# Environment
VECTOR_DB=qdrant
QDRANT_URL=http://localhost:6333
```

### Milvus

```bash
pip install pymilvus

VECTOR_DB=milvus
MILVUS_HOST=localhost
MILVUS_PORT=19530
```

---

## Storage Backends

### AWS S3

```bash
pip install boto3

# Environment
STORAGE_PROVIDER=s3
S3_BUCKET_NAME=my-bucket
AWS_ACCESS_KEY_ID=...
AWS_SECRET_ACCESS_KEY=...
AWS_REGION=us-east-1
```

### Azure Blob Storage

```bash
pip install azure-storage-blob

STORAGE_PROVIDER=azure
AZURE_STORAGE_ACCOUNT=...
AZURE_STORAGE_KEY=...
AZURE_CONTAINER=openwebui
```

---

## Authentication Providers

### OAuth (Google, Azure AD)

**Google OAuth:**
```python
OAUTH_GOOGLE_CLIENT_ID=...
OAUTH_GOOGLE_CLIENT_SECRET=...
```

**Azure AD:**
```python
OAUTH_AZURE_CLIENT_ID=...
OAUTH_AZURE_CLIENT_SECRET=...
OAUTH_AZURE_TENANT_ID=...
```

### LDAP

```python
LDAP_SERVER_URL=ldap://ldap.example.com
LDAP_BIND_DN=cn=admin,dc=example,dc=com
LDAP_BIND_PASSWORD=...
LDAP_USER_BASE=ou=users,dc=example,dc=com
```

---

## Image Generation

### AUTOMATIC1111

```python
AUTOMATIC1111_BASE_URL=http://localhost:7860
IMAGE_GENERATION_ENGINE=automatic1111
```

### ComfyUI

```python
COMFYUI_BASE_URL=http://localhost:8188
IMAGE_GENERATION_ENGINE=comfyui
```

### OpenAI DALL-E

```python
IMAGE_GENERATION_ENGINE=openai
IMAGES_OPENAI_API_KEY=...
```

---

**Last updated:** November 18, 2025
