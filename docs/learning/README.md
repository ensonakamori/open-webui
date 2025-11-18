# Open WebUI Learning Path 🎓

**Welcome!** This is your comprehensive guide to learning and contributing to Open WebUI.

**Documented:** November 18, 2025
**Tech Stack Research:** [TECH_STACK_RESEARCH.md](./TECH_STACK_RESEARCH.md)
**Status:** ✅ Current as of November 2025

---

## 🚀 Quick Start

**New to the project?** Start here:

1. 📖 Read [GETTING_STARTED.md](./GETTING_STARTED.md) - Set up your development environment
2. 🏗️ Read [ARCHITECTURE_OVERVIEW.md](./ARCHITECTURE_OVERVIEW.md) - Understand the big picture
3. 🗺️ Read [PROJECT_STRUCTURE.md](./PROJECT_STRUCTURE.md) - Learn where everything lives
4. 🎯 Pick your path below based on your role

**Time investment:** 2-3 hours to get oriented, 1 week to first contribution

---

## 🎯 Learning Paths by Role

### 🎨 Frontend Developer (React → Svelte)

**Goal:** Contribute to the Svelte/TypeScript frontend

1. ✅ [GETTING_STARTED.md](./GETTING_STARTED.md) - Setup
2. ✅ [TECH_STACK_GUIDE.md](./TECH_STACK_GUIDE.md) - Focus on:
   - **Svelte 5 Runes** (🚨 Different from React hooks!)
   - **SvelteKit** (Similar to Next.js)
   - **Tailwind CSS v4** (🆕 Brand new config approach!)
3. ✅ [FRONTEND_ARCHITECTURE.md](./FRONTEND_ARCHITECTURE.md) - Deep dive
4. ✅ [DATA_FLOW_GUIDE.md](./DATA_FLOW_GUIDE.md) - How data moves
5. ✅ [CODE_TOURS.md](./CODE_TOURS.md) - Walk through real features
6. ✅ [PATTERNS_AND_CONVENTIONS.md](./PATTERNS_AND_CONVENTIONS.md) - Code style
7. ✅ [HOW_TO_GUIDE.md](./HOW_TO_GUIDE.md) - Add your first feature

**Key Challenges:**
- 🧠 **Svelte 5 Runes** are NOT like React hooks (mental shift required)
- 🧠 **No virtual DOM** (reactivity works differently)
- 🧠 **Stores** for global state (not Redux/Zustand)
- 🧠 **Tailwind v4** uses CSS-first config (no `tailwind.config.js`)

**Estimated Time:** 3-5 days to proficiency

---

### ⚙️ Backend Developer (Express/Django → FastAPI)

**Goal:** Contribute to the Python/FastAPI backend

1. ✅ [GETTING_STARTED.md](./GETTING_STARTED.md) - Setup
2. ✅ [TECH_STACK_GUIDE.md](./TECH_STACK_GUIDE.md) - Focus on:
   - **FastAPI** (Similar to Express but Python)
   - **SQLAlchemy 2.0** (🚨 Different from 1.x!)
   - **Pydantic v2** (Type validation)
3. ✅ [BACKEND_ARCHITECTURE.md](./BACKEND_ARCHITECTURE.md) - Deep dive
4. ✅ [DATABASE_ARCHITECTURE.md](./DATABASE_ARCHITECTURE.md) - Data layer
5. ✅ [INTEGRATION_GUIDE.md](./INTEGRATION_GUIDE.md) - External services
6. ✅ [API_DOCUMENTATION.md](./API_DOCUMENTATION.md) - API contracts
7. ✅ [HOW_TO_GUIDE.md](./HOW_TO_GUIDE.md) - Add your first endpoint

**Key Challenges:**
- 🧠 **SQLAlchemy 2.0** uses completely new syntax
- 🧠 **Async/await** patterns throughout
- 🧠 **LangChain v0.3** (🚨 Project uses deprecated version)
- 🧠 **FastAPI dependency injection** (powerful but different)

**Estimated Time:** 3-5 days to proficiency

---

### 🤖 AI/ML Developer

**Goal:** Work on LLM integrations, RAG, embeddings

1. ✅ [GETTING_STARTED.md](./GETTING_STARTED.md) - Setup
2. ✅ [ARCHITECTURE_OVERVIEW.md](./ARCHITECTURE_OVERVIEW.md) - System design
3. ✅ [INTEGRATION_GUIDE.md](./INTEGRATION_GUIDE.md) - Focus on:
   - LLM providers (Ollama, OpenAI, Anthropic)
   - Vector databases (ChromaDB)
   - **LangChain** (⚠️ v0.3 in use, v1.0 recommended)
4. ✅ [CODE_TOURS.md](./CODE_TOURS.md) - Walk through RAG query flow
5. ✅ [HOW_TO_GUIDE.md](./HOW_TO_GUIDE.md) - Add a new LLM provider
6. 📄 Backend routers:
   - [retrieval.py](../../backend/open_webui/routers/retrieval.py) - RAG implementation
   - [ollama.py](../../backend/open_webui/routers/ollama.py) - Ollama integration
   - [openai.py](../../backend/open_webui/routers/openai.py) - OpenAI integration

**Key Challenges:**
- 🧠 **RAG architecture** (embeddings, retrieval, reranking)
- 🧠 **Streaming responses** (Server-Sent Events)
- 🧠 **Vector database** operations
- 🧠 **LangChain v0.3** (deprecated, migration needed)

**Estimated Time:** 5-7 days to proficiency

---

### 🎓 Complete Learner (New to Everything)

**Goal:** Learn modern full-stack development from scratch

**Week 1: Foundations**
1. [TECH_STACK_RESEARCH.md](./TECH_STACK_RESEARCH.md) - What's current?
2. [GETTING_STARTED.md](./GETTING_STARTED.md) - Get it running
3. [ARCHITECTURE_OVERVIEW.md](./ARCHITECTURE_OVERVIEW.md) - Big picture
4. [PROJECT_STRUCTURE.md](./PROJECT_STRUCTURE.md) - File organization
5. [TECH_STACK_GUIDE.md](./TECH_STACK_GUIDE.md) - Technologies explained

**Week 2: Frontend**
6. [FRONTEND_ARCHITECTURE.md](./FRONTEND_ARCHITECTURE.md) - Svelte 5 + SvelteKit
7. [PATTERNS_AND_CONVENTIONS.md](./PATTERNS_AND_CONVENTIONS.md) - Code style
8. [CODE_TOURS.md](./CODE_TOURS.md) - Real code walkthroughs

**Week 3: Backend**
9. [BACKEND_ARCHITECTURE.md](./BACKEND_ARCHITECTURE.md) - FastAPI
10. [DATABASE_ARCHITECTURE.md](./DATABASE_ARCHITECTURE.md) - Data layer
11. [API_DOCUMENTATION.md](./API_DOCUMENTATION.md) - API contracts

**Week 4: Advanced Topics**
12. [INTEGRATION_GUIDE.md](./INTEGRATION_GUIDE.md) - External services
13. [SECURITY_GUIDE.md](./SECURITY_GUIDE.md) - Security best practices
14. [DATA_FLOW_GUIDE.md](./DATA_FLOW_GUIDE.md) - How it all connects

**Week 5: Contributing**
15. [HOW_TO_GUIDE.md](./HOW_TO_GUIDE.md) - Build features
16. [TESTING_GUIDE.md](./TESTING_GUIDE.md) - Write tests
17. [FIRST_CONTRIBUTIONS.md](./FIRST_CONTRIBUTIONS.md) - Your first PR

**Estimated Time:** 1 month to first meaningful contribution

---

## 📚 All Learning Resources

### 🏗️ Foundation (Start Here)

| Document | Purpose | Time | Prerequisites |
|----------|---------|------|---------------|
| [GETTING_STARTED.md](./GETTING_STARTED.md) | Setup dev environment | 30 min | Basic terminal knowledge |
| [ARCHITECTURE_OVERVIEW.md](./ARCHITECTURE_OVERVIEW.md) | System design | 1 hour | None |
| [PROJECT_STRUCTURE.md](./PROJECT_STRUCTURE.md) | File organization | 30 min | None |
| [TECH_STACK_GUIDE.md](./TECH_STACK_GUIDE.md) | Technologies explained | 2 hours | JS/Python basics |
| [DATA_FLOW_GUIDE.md](./DATA_FLOW_GUIDE.md) | Request/response flows | 1 hour | Architecture Overview |

### 🔍 Deep Dives (Technology-Specific)

| Document | Purpose | Time | Prerequisites |
|----------|---------|------|---------------|
| [FRONTEND_ARCHITECTURE.md](./FRONTEND_ARCHITECTURE.md) | Svelte 5 + SvelteKit | 2 hours | JS/TS basics |
| [BACKEND_ARCHITECTURE.md](./BACKEND_ARCHITECTURE.md) | FastAPI + SQLAlchemy | 2 hours | Python basics |
| [DATABASE_ARCHITECTURE.md](./DATABASE_ARCHITECTURE.md) | Database schema | 1 hour | SQL basics |
| [INTEGRATION_GUIDE.md](./INTEGRATION_GUIDE.md) | External services | 1 hour | Backend Architecture |

### 🛠️ Practical Guides (Hands-On)

| Document | Purpose | Time | Prerequisites |
|----------|---------|------|---------------|
| [PATTERNS_AND_CONVENTIONS.md](./PATTERNS_AND_CONVENTIONS.md) | Code style guide | 1 hour | None |
| [HOW_TO_GUIDE.md](./HOW_TO_GUIDE.md) | Build features | 2 hours | Architecture docs |
| [CODE_TOURS.md](./CODE_TOURS.md) | Walkthrough examples | 2 hours | Project Structure |
| [DEVELOPMENT_WORKFLOW.md](./DEVELOPMENT_WORKFLOW.md) | Git/testing/debugging | 1 hour | Getting Started |

### 📖 Reference (When Needed)

| Document | Purpose | Time | Prerequisites |
|----------|---------|------|---------------|
| [TESTING_GUIDE.md](./TESTING_GUIDE.md) | Write tests | 1 hour | How-To Guide |
| [DEBUGGING_GUIDE.md](./DEBUGGING_GUIDE.md) | Debug issues | 1 hour | None |
| [SECURITY_GUIDE.md](./SECURITY_GUIDE.md) | Security practices | 1 hour | Backend Architecture |
| [API_DOCUMENTATION.md](./API_DOCUMENTATION.md) | API reference | 30 min | None |
| [DATABASE_SCHEMA.md](./DATABASE_SCHEMA.md) | Schema reference | 30 min | Database Architecture |

### 🎯 Practice (Apply Knowledge)

| Document | Purpose | Time | Prerequisites |
|----------|---------|------|---------------|
| [EXERCISES.md](./EXERCISES.md) | Hands-on exercises | Varies | Depends on exercise |
| [FIRST_CONTRIBUTIONS.md](./FIRST_CONTRIBUTIONS.md) | Contribution guide | 30 min | All foundation docs |
| [FAQ.md](./FAQ.md) | Common questions | As needed | None |

---

## 🎓 Key Concepts to Master

### 🔴 Critical (Must Learn)

1. **Svelte 5 Runes** - New reactivity paradigm
   - 📍 Location: [FRONTEND_ARCHITECTURE.md](./FRONTEND_ARCHITECTURE.md)
   - 🧠 Mental model: More explicit than React hooks
   - ⏱️ Time: 2-3 hours
   - 🔗 Files: All `*.svelte` files

2. **SvelteKit Routing** - File-based routing
   - 📍 Location: [FRONTEND_ARCHITECTURE.md](./FRONTEND_ARCHITECTURE.md)
   - 🧠 Mental model: Like Next.js App Router
   - ⏱️ Time: 1 hour
   - 🔗 Files: [src/routes/](../../src/routes/)

3. **FastAPI Patterns** - Async Python web framework
   - 📍 Location: [BACKEND_ARCHITECTURE.md](./BACKEND_ARCHITECTURE.md)
   - 🧠 Mental model: Like Express but with types
   - ⏱️ Time: 2 hours
   - 🔗 Files: [backend/open_webui/routers/](../../backend/open_webui/routers/)

4. **SQLAlchemy 2.0** - Modern ORM patterns
   - 📍 Location: [DATABASE_ARCHITECTURE.md](./DATABASE_ARCHITECTURE.md)
   - 🧠 Mental model: Different from 1.x!
   - ⏱️ Time: 2 hours
   - 🔗 Files: [backend/open_webui/models/](../../backend/open_webui/models/)

### 🟡 Important (Should Learn)

5. **Tailwind CSS v4** - Utility-first CSS
   - 📍 Location: [TECH_STACK_GUIDE.md](./TECH_STACK_GUIDE.md)
   - 🧠 Mental model: CSS-first config (new in v4!)
   - ⏱️ Time: 1 hour
   - 🔗 Files: [tailwind.config.js](../../tailwind.config.js)

6. **RAG Architecture** - Vector search + LLMs
   - 📍 Location: [INTEGRATION_GUIDE.md](./INTEGRATION_GUIDE.md)
   - 🧠 Mental model: Embed → Store → Retrieve → Generate
   - ⏱️ Time: 3 hours
   - 🔗 Files: [retrieval.py](../../backend/open_webui/routers/retrieval.py)

7. **WebSocket Patterns** - Real-time collaboration
   - 📍 Location: [BACKEND_ARCHITECTURE.md](./BACKEND_ARCHITECTURE.md)
   - 🧠 Mental model: Bidirectional event streams
   - ⏱️ Time: 2 hours
   - 🔗 Files: [backend/open_webui/socket/](../../backend/open_webui/socket/)

8. **State Management** - Svelte stores
   - 📍 Location: [FRONTEND_ARCHITECTURE.md](./FRONTEND_ARCHITECTURE.md)
   - 🧠 Mental model: Simpler than Redux
   - ⏱️ Time: 1 hour
   - 🔗 Files: [src/lib/stores/](../../src/lib/stores/)

### 🟢 Nice to Know (Optional)

9. **Pyodide** - Python in browser
10. **TipTap** - Rich text editor
11. **CodeMirror** - Code editor
12. **i18next** - Internationalization

---

## ⚠️ Important Notices

### 🚨 Version-Specific Warnings

**LangChain v0.3 → v1.0**
- ⚠️ This project uses LangChain v0.3 which is DEPRECATED
- ✅ LangChain v1.0 is stable and recommended
- 📍 See [TECH_STACK_RESEARCH.md](./TECH_STACK_RESEARCH.md#ai-llm-framework-langchain-v0327) for details
- 🎯 Migration guide coming in [INTEGRATION_GUIDE.md](./INTEGRATION_GUIDE.md)

**Tailwind CSS v4 (Brand New!)**
- 🆕 Released January 2025 - Very recent!
- ⚠️ Most online tutorials show v3 patterns - ignore them
- ✅ This project uses v4 - learn the new patterns
- 📍 See [TECH_STACK_RESEARCH.md](./TECH_STACK_RESEARCH.md#styling-tailwind-css-v400)

**Svelte 5 Runes (Paradigm Shift!)**
- 🆕 Released October 2024
- ⚠️ Completely different from Svelte 3/4 patterns
- 🚫 Ignore tutorials about `$:` reactive statements - that's old
- ✅ Learn `$state`, `$derived`, `$effect` instead
- 📍 See [TECH_STACK_RESEARCH.md](./TECH_STACK_RESEARCH.md#frontend-framework-svelte-v500)

**SQLAlchemy 2.0**
- ⚠️ Completely different from SQLAlchemy 1.x
- 🚫 Old `Query` API is deprecated
- ✅ Use new `select()` syntax
- 📍 See [DATABASE_ARCHITECTURE.md](./DATABASE_ARCHITECTURE.md)

---

## 🗺️ Navigation Tips

### Finding Code Examples

All documentation links directly to code:
```markdown
[Chat component](../../src/lib/components/chat/Chat.svelte#L45-L67)
```

Click the link to see the actual implementation!

### Understanding Markers

Throughout the docs, you'll see:
- ✅ **CURRENT (Nov 2025)** - Up-to-date pattern
- ⚠️ **OUTDATED PATTERN** - Works but newer exists
- 🚨 **DEPRECATED** - Don't use in new code
- 🆕 **NEW IN 2025** - Recently introduced
- 🔍 **NEEDS VERIFICATION** - Uncertain, investigate
- ❓ **ASSUMPTION** - Inference, not confirmed

### When You're Stuck

1. Check [FAQ.md](./FAQ.md) first
2. Search for keywords in relevant docs
3. Look at [CODE_TOURS.md](./CODE_TOURS.md) for examples
4. Use [DEBUGGING_GUIDE.md](./DEBUGGING_GUIDE.md)
5. Ask in Discord/GitHub discussions

---

## 🤝 Contributing to Docs

Found an error? Have a suggestion?

1. Docs live in `docs/learning/`
2. All docs are Markdown
3. Include code links, analogies, examples
4. Use currency markers appropriately
5. Submit a PR!

**Documentation philosophy:**
- ✅ Admit when uncertain
- ✅ Link to actual code
- ✅ Provide investigation paths
- ✅ Use analogies to familiar tech
- ❌ Never fabricate or guess

---

## 📊 Tech Stack at a Glance

| Category | Technology | Status | Learning Priority |
|----------|-----------|--------|------------------|
| Frontend Framework | Svelte 5 | ✅ Current | 🔴 Critical |
| Frontend Meta-framework | SvelteKit 2 | ⚠️ Behind latest | 🔴 Critical |
| Backend Framework | FastAPI 0.118 | ✅ Current | 🔴 Critical |
| Language (Frontend) | TypeScript 5.5 | ⚠️ Behind latest | 🟡 Important |
| Language (Backend) | Python 3.11-3.12 | ✅ Current | 🔴 Critical |
| Database ORM | SQLAlchemy 2.0 | ✅ Current | 🔴 Critical |
| Styling | Tailwind CSS 4 | ✅ Latest | 🟡 Important |
| Build Tool | Vite 5 | ⚠️ Behind latest | 🟢 Nice to know |
| LLM Framework | LangChain 0.3 | 🚨 Deprecated | 🟡 Important |
| Vector DB | ChromaDB 1.0 | ⚠️ Behind latest | 🟡 Important |
| Rich Text | TipTap 3 | ⚠️ Behind latest | 🟢 Nice to know |

**Full research:** [TECH_STACK_RESEARCH.md](./TECH_STACK_RESEARCH.md)

---

## 🎯 Success Criteria

After completing the learning path, you should be able to:

✅ Set up the development environment independently
✅ Navigate the codebase confidently
✅ Understand Svelte 5 Runes (even coming from React)
✅ Understand FastAPI patterns (even coming from Express)
✅ Make your first contribution within 1-2 weeks
✅ Debug common issues
✅ Write tests for new features
✅ Understand the RAG implementation
✅ Add a new LLM provider
✅ Add a new frontend feature
✅ Add a new backend endpoint

---

## 📞 Getting Help

- 💬 **Discord:** [Community link]
- 🐛 **Issues:** [GitHub Issues](https://github.com/open-webui/open-webui/issues)
- 💡 **Discussions:** [GitHub Discussions](https://github.com/open-webui/open-webui/discussions)
- 📖 **Main Docs:** [README.md](../../README.md)
- 🔧 **Contributing:** [CONTRIBUTING.md](../CONTRIBUTING.md)

---

## 🚀 Ready to Start?

**Recommended first steps:**

1. 📖 [GETTING_STARTED.md](./GETTING_STARTED.md) ← Start here!
2. 🏗️ [ARCHITECTURE_OVERVIEW.md](./ARCHITECTURE_OVERVIEW.md)
3. 🗺️ [PROJECT_STRUCTURE.md](./PROJECT_STRUCTURE.md)
4. Choose your learning path above ⬆️

---

**Happy Learning! 🎓**

*Last updated: November 18, 2025*
*Next review: January 2026 or major version changes*
