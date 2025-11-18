# Technology Stack Research (November 2025)

**Research conducted:** November 18, 2025
**AI Knowledge cutoff:** January 2025
**Purpose:** Ensure all learning materials reflect current best practices and identify outdated patterns

---

## Executive Summary

Open WebUI is a modern full-stack application combining:
- **Frontend:** Svelte 5 + SvelteKit 2 + TypeScript
- **Backend:** Python FastAPI + SQLAlchemy/Peewee
- **Key Features:** AI/LLM integration, real-time collaboration, rich text editing, vector search
- **Deployment:** Docker, Node.js, Python environments

**Overall Status:** ✅ Project uses current and well-maintained technologies. Most patterns are up-to-date as of November 2025.

---

## Technologies Used in This Project

### Frontend Framework: Svelte - v5.0.0

**Current Status (Nov 2025):**
- Latest stable: v5.43.8 (November 2025)
- Project uses: v5.0.0
- Status: ⚠️ **Slightly outdated** - 43 patch releases behind

**Important Updates Since Jan 2025:**
- Svelte 5.0 was officially released on October 19, 2024 (before my cutoff but worth noting)
- November 2025 releases added:
  - `createContext` feature (v5.40.0)
  - `$state.eager` rune (v5.41.0)
  - `fork` API for managing state changes (v5.42.0)
- Continued refinements to the Runes API (Svelte 5's new reactivity system)
- Multiple performance improvements and bug fixes
- Enhanced TypeScript support

**What This Means for Learning:**
- ✅ The project uses Svelte 5, which is the CURRENT paradigm
- Svelte 5 introduced "Runes" - a fundamental change in how reactivity works
- **Key Runes to learn:**
  - `$state` - reactive state
  - `$derived` - computed values
  - `$effect` - side effects
  - `$props` - component props
- This is a major departure from Svelte 3/4 patterns (stores, `$:` reactive statements)
- All learning materials should focus on Runes, not legacy patterns

**Official Resources:**
- Docs: https://svelte.dev/docs/svelte/overview
- Tutorial: https://svelte.dev/tutorial/svelte/welcome-to-svelte
- Blog (What's New): https://svelte.dev/blog/whats-new-in-svelte-november-2025
- Migration Guide: https://svelte.dev/docs/svelte/v5-migration-guide
- GitHub Releases: https://github.com/sveltejs/svelte/releases

---

### Frontend Framework: SvelteKit - v2.5.27

**Current Status (Nov 2025):**
- Latest stable: v2.48.5 (November 14, 2025)
- Project uses: v2.5.27
- Status: 🚨 **Significantly behind** - ~43 minor releases behind

**Important Updates Since Jan 2025:**
- **Remote Functions** (experimental): Write server-only logic in `.remote.ts` files and call them from components with full type safety
- **Svelte MCP**: Official Svelte MCP server to help AI assistants write valid Svelte 5 code
- Improved form validation and handling
- Enhanced query states and typed params
- Better prerendering and caching in development
- Multiple performance improvements and bug fixes

**What This Means for Learning:**
- ✅ SvelteKit 2 is still the current major version
- The project may not have the latest features like Remote Functions
- Core SvelteKit patterns (routing, loading, actions) remain stable
- Remote Functions are experimental - not necessary for learning core concepts

**Official Resources:**
- Docs: https://svelte.dev/docs/kit/introduction
- Migration Guide: https://svelte.dev/docs/kit/migrating-to-sveltekit-2
- Changelog: https://github.com/sveltejs/kit/blob/main/packages/kit/CHANGELOG.md
- GitHub Releases: https://github.com/sveltejs/kit/releases

---

### Build Tool: Vite - v5.4.14

**Current Status (Nov 2025):**
- Latest stable: v6.0.0 (released November 26, 2024)
- Project uses: v5.4.14
- Status: 🚨 **One major version behind**

**Important Updates Since Jan 2025:**
- **Vite 6.0** was released (Nov 2024, just before my cutoff ended)
- **Environment API**: New experimental API for more flexible build configurations
- Support for running on different targets (Node.js, Cloudflare Workerd, etc.)
- Performance improvements across the board
- Node.js 18, 20, and 22+ support (Node 21 dropped)

**What This Means for Learning:**
- ⚠️ Project uses Vite 5, which is still widely used
- Vite 6 is relatively new (released Nov 2024)
- The core Vite concepts remain the same
- Most Vite 5 patterns work identically in Vite 6
- Upgrading to Vite 6 would be beneficial but not critical

**Official Resources:**
- Docs: https://vite.dev/
- V6 Announcement: https://vite.dev/blog/announcing-vite6
- Migration Guide: https://vite.dev/guide/migration
- GitHub: https://github.com/vitejs/vite

---

### Language: TypeScript - v5.5.4

**Current Status (Nov 2025):**
- Latest stable: v5.9.0 (August 1, 2025)
- Project uses: v5.5.4
- Status: ⚠️ **4 minor versions behind**

**Important Updates Since Jan 2025:**
- **TypeScript 5.6** (Sept 2024): Enhanced nullish/truthy checks, `--noUncheckedSideEffectImports`
- **TypeScript 5.7** (Nov 2024): Better uninitialized variable detection, `--rewriteRelativeImportExtensions`
- **TypeScript 5.8** (Mar 2025): Node.js compile cache API - **2-3x faster build times**
- **TypeScript 5.9** (Aug 2025): Latest stable version

**What This Means for Learning:**
- ✅ TypeScript 5.5+ is modern and current
- Missing out on significant performance improvements from 5.8+
- Core TypeScript patterns remain the same
- All type safety features work as expected

**Official Resources:**
- Docs: https://www.typescriptlang.org/docs/
- Handbook: https://www.typescriptlang.org/docs/handbook/intro.html
- Release Notes: https://devblogs.microsoft.com/typescript/
- What's New: https://github.com/microsoft/TypeScript/releases

---

### Styling: Tailwind CSS - v4.0.0

**Current Status (Nov 2025):**
- Latest stable: v4.0.0 (released January 22, 2025)
- Project uses: v4.0.0
- Status: ✅ **CURRENT** - Using the latest major version

**Important Updates Since Jan 2025:**
- **Tailwind v4** was released January 22, 2025 (recent!)
- 🆕 **NEW IN 2025:** Complete rewrite of the engine
- **Performance:** Full builds 5x faster, incremental builds 100x+ faster
- **CSS-First Configuration:** No more `tailwind.config.js` - configure in CSS with `@import "tailwindcss"`
- **Automatic Content Detection:** Template files discovered automatically
- **Native CSS Variables:** Design tokens as CSS custom properties
- **Container Queries:** Built-in (no plugin needed)
- **3D Transforms:** `rotate-x-*`, `rotate-y-*`, `scale-z-*`, `translate-z-*`
- **Gradients:** Linear, radial, and conic with advanced controls
- **Modern CSS:** Uses cascade layers, `@property`, `color-mix()`
- **Vite Plugin:** First-party integration

**What This Means for Learning:**
- ✅ **EXCELLENT** - Project is on the cutting edge
- This is a MAJOR paradigm shift from Tailwind v3
- Configuration approach is completely different
- All learning materials should use v4 patterns
- ⚠️ Most online tutorials still show v3 patterns - ignore those

**Official Resources:**
- Docs: https://tailwindcss.com/docs
- V4 Announcement: https://tailwindcss.com/blog/tailwindcss-v4
- GitHub: https://github.com/tailwindlabs/tailwindcss

---

### Backend Framework: FastAPI - v0.118.0

**Current Status (Nov 2025):**
- Latest stable: v0.121.0 (November 3, 2025)
- Project uses: v0.118.0
- Status: ⚠️ **3 minor versions behind**

**Important Updates Since Jan 2025:**
- ✅ **No breaking changes** in recent releases
- 🚨 **IMPORTANT DEPRECATION:** Pydantic v1 support is deprecated
  - Will be removed from FastAPI soon
  - Pydantic team stopped supporting v1 for Python 3.14+
  - FastAPI temporarily supports both v1 and v2 for migration
- Internal refactoring and improvements
- Migration from `typing_extensions.Doc` to `annotated_doc.Doc`
- Bug fixes for security schemes and dependencies

**What This Means for Learning:**
- ✅ FastAPI patterns are stable and current
- **ACTION REQUIRED:** Ensure using Pydantic v2 (not v1)
- All core FastAPI concepts remain unchanged
- Learning materials should emphasize Pydantic v2

**Official Resources:**
- Docs: https://fastapi.tiangolo.com/
- Release Notes: https://fastapi.tiangolo.com/release-notes/
- Tutorial: https://fastapi.tiangolo.com/tutorial/
- GitHub: https://github.com/fastapi/fastapi

---

### Python Version: 3.11 - 3.12

**Current Status (Nov 2025):**
- Latest Python 3.11: v3.11.14 (October 9, 2025)
- Latest Python 3.12: v3.12.12 (October 9, 2025)
- Latest Python 3.13: v3.13.5 (June 11, 2025)
- Latest Python 3.14: Released November 2025
- Project requires: Python 3.11 - 3.12
- Status: ⚠️ **One major version behind** (3.13 available, 3.14 just released)

**Important Updates Since Jan 2025:**
- **Python 3.11 & 3.12:** Now in "security fixes only" mode
  - 3.11: Security support until October 2027
  - 3.12: Security support until October 2028
- **Python 3.13:** Latest stable feature release
- **Python 3.14:** Just released November 2025
  - Modernized REPL with real-time syntax highlighting
  - `concurrent.interpreters` module for multiple interpreter support
- Security patches for tarfile, unicode-escape decoder, IPv6 processing

**What This Means for Learning:**
- ✅ Python 3.11-3.12 are still well-supported
- Project is stable but not cutting-edge
- Consider upgrading to 3.13 in the future
- All modern Python patterns work fine

**Official Resources:**
- Docs: https://docs.python.org/3/
- What's New in 3.12: https://docs.python.org/3/whatsnew/3.12.html
- What's New in 3.13: https://docs.python.org/3/whatsnew/3.13.html
- Release Schedule: https://devguide.python.org/versions/

---

### ORM: SQLAlchemy - v2.0.38

**Current Status (Nov 2025):**
- Latest stable: v2.0.44 (October 10, 2025)
- Project uses: v2.0.38
- Status: ⚠️ **6 patch versions behind**

**Important Updates Since Jan 2025:**
- Adjustments for Python 3.14 beta support
- PostgreSQL: Support for `postgresql_include` in constraints
- SQLite: Improvements to CHECK constraint reflection
- Various bug fixes and performance improvements
- Continued refinements to SQLAlchemy 2.0 patterns

**What This Means for Learning:**
- ✅ SQLAlchemy 2.0 is THE current paradigm
- This is a major change from SQLAlchemy 1.x
- All learning should focus on 2.0 patterns
- Key changes: `select()` syntax, async support, typing

**Official Resources:**
- Docs: https://docs.sqlalchemy.org/en/20/
- What's New in 2.0: https://docs.sqlalchemy.org/en/20/changelog/whatsnew_20.html
- Tutorial: https://docs.sqlalchemy.org/en/20/tutorial/index.html
- Migration Guide: https://docs.sqlalchemy.org/en/20/changelog/migration_20.html

---

### ORM (Alternative): Peewee - v3.18.1

**Current Status (Nov 2025):**
- ⚠️ UNCLEAR: Unable to find November 2025 releases
- Project uses: v3.18.1
- Status: 🔍 **Needs investigation** - May be legacy/maintenance mode

**What This Means for Learning:**
- Peewee is a simpler, lighter alternative to SQLAlchemy
- Often used alongside SQLAlchemy in projects
- Less feature-rich but easier to learn
- Good for simple queries and small-scale operations

**Official Resources:**
- Docs: http://docs.peewee-orm.com/
- GitHub: https://github.com/coleifer/peewee

---

### AI/LLM Framework: LangChain - v0.3.27

**Current Status (Nov 2025):**
- Latest stable: v1.0.7 (November 14, 2025)
- Project uses: v0.3.27
- Status: 🚨 **MAJOR VERSION BEHIND** - LangChain has reached 1.0!

**Important Updates Since Jan 2025:**
- 🆕 **LangChain 1.0** released (stable as of Nov 2025)
- **Breaking change:** `create_react_agent` deprecated in favor of `create_agent`
- **Python 3.9 dropped** - requires Python 3.10+ (EOL October 2025)
- **Scope reduction:** Core package focuses on essential abstractions
  - Legacy functionality moved to `langchain-classic`
- **Content blocks:** New `.content_blocks` property for structured LLM responses
- **Stability commitment:** No breaking changes until 2.0
- LangGraph 1.0 also released alongside

**What This Means for Learning:**
- 🚨 **CRITICAL:** Project uses deprecated v0.3 API
- Migration to 1.0 recommended
- v0.3 patterns may be outdated
- Learning materials should note the version difference
- **Recommend:** Document both v0.3 (current) and v1.0 (target) patterns

**Official Resources:**
- Docs: https://python.langchain.com/
- V1.0 Announcement: https://blog.langchain.com/langchain-langchain-1-0-alpha-releases/
- Release Notes: https://github.com/langchain-ai/langchain/releases
- Migration Guide: https://python.langchain.com/docs/versions/release_policy/

---

### Vector Database: ChromaDB - v1.0.20

**Current Status (Nov 2025):**
- Latest stable: v1.3.3 (November 5, 2025)
- Project uses: v1.0.20
- Status: 🚨 **3 minor versions behind**

**Important Updates Since Jan 2025:**
- ChromaDB progressed from 1.0.x to 1.3.x
- November 2025 releases: 1.3.0 (Oct 29), 1.3.3 (Nov 5)
- Now in top 5% of popular vector databases
- 23.3K GitHub stars in 2025
- Active development and improvements

**What This Means for Learning:**
- ✅ ChromaDB 1.x is current
- Project is slightly behind but functional
- Core concepts remain stable
- Excellent for vector search and embeddings

**Official Resources:**
- Docs: https://docs.trychroma.com/
- Cookbook: https://cookbook.chromadb.dev/
- GitHub: https://github.com/chroma-core/chroma

---

### Rich Text Editor: TipTap - v3.0.7

**Current Status (Nov 2025):**
- Latest stable: v3.10.7 (November 13, 2025)
- Project uses: v3.0.7
- Status: ⚠️ **10 minor versions behind**

**Important Updates Since Jan 2025:**
- TipTap 3.0 went stable in July 2025
- 🆕 **NEW IN 2025:**
  - MarkViews for custom HTML rendering of marks
  - ResizableNodeView with configurable resize handles
  - Static Renderer for JSON → HTML/Markdown/React without editor instance
  - Improved mobile touch events
  - Enhanced transactions and performance
- Active development with frequent updates

**What This Means for Learning:**
- ✅ TipTap 3.0 is THE current version
- This is a major upgrade from TipTap 2.x
- Missing some newer features but core functionality intact
- Documentation is actively maintained

**Official Resources:**
- Docs: https://tiptap.dev/docs/editor/introduction
- V3.0 Announcement: https://tiptap.dev/blog/release-notes/tiptap-3-0-is-stable
- GitHub: https://github.com/ueberdosis/tiptap

---

### Machine Learning: Transformers (HuggingFace)

**Current Status (Nov 2025):**
- Latest stable (Python): v4.57.1 (October 14, 2025)
- Latest stable (JS): v3.7.6
- Project uses: `@huggingface/transformers` v3.0.0 (JavaScript)
- Status: ⚠️ **7 minor versions behind** (JS version)

**Important Updates Since Jan 2025:**
- **Python library** is at v4.57.x (not v3.x)
- **JavaScript library** (@huggingface/transformers) is at v3.7.6
- New models: EmbeddingGemma, VaultGemma
- Active development and frequent updates
- State-of-the-art model support

**What This Means for Learning:**
- Project uses the JavaScript version of Transformers
- Enables running ML models in the browser
- Core concepts are stable
- Excellent for client-side AI features

**Official Resources:**
- Docs: https://huggingface.co/docs/transformers
- JS Docs: https://huggingface.co/docs/transformers.js
- GitHub (Python): https://github.com/huggingface/transformers
- GitHub (JS): https://github.com/huggingface/transformers.js

---

### Python in Browser: Pyodide - v0.28.2

**Current Status (Nov 2025):**
- Latest in 0.28 series: v0.28.3 (September 22, 2025)
- Latest overall: v0.29.0 (October 20, 2025)
- Project uses: v0.28.2
- Status: ⚠️ **One minor version behind**

**Important Updates Since Jan 2025:**
- **Pyodide 0.28** (July 2025): Built with Python 3.13
- **Pyodide 0.29** (October 2025): Latest release
- New ABI based on Emscripten 4.0.9
- Focus on platform standardization
- Enables running Python in the browser via WebAssembly

**What This Means for Learning:**
- ✅ Pyodide enables unique browser-based Python execution
- Project is reasonably current
- Exciting technology for AI/data science in the browser
- Well-suited for Open WebUI's use case

**Official Resources:**
- Docs: https://pyodide.org/en/stable/
- Changelog: https://pyodide.org/en/stable/project/changelog.html
- GitHub: https://github.com/pyodide/pyodide

---

### Runtime: Node.js - v18-22

**Current Status (Nov 2025):**
- Node.js 22.x: In Maintenance LTS (codename "Jod")
- Node.js 24.x: Active LTS (codename "Krypton")
- Latest 22.x: v22.21.0 (October 20, 2025)
- Project requires: Node.js 18-22
- Status: ✅ **CURRENT** - Supports latest LTS

**Important Updates Since Jan 2025:**
- **Node.js 22** transitioned to Maintenance LTS (Oct 2025)
- **Node.js 24** is now Active LTS
- Node.js 22 bundles OpenSSL 3.5.2
- Supported until April 2027
- Project's range (18-22) is appropriate

**What This Means for Learning:**
- ✅ Node.js support is current and correct
- No concerns or migration needed
- All modern Node.js features available

**Official Resources:**
- Docs: https://nodejs.org/docs/latest/api/
- Release Schedule: https://github.com/nodejs/Release
- Download: https://nodejs.org/

---

## Summary: Upgrade Recommendations

### 🚨 High Priority (Breaking/Deprecated)
1. **LangChain 0.3 → 1.0** - Major version available, v0.3 APIs may be deprecated
2. **SvelteKit 2.5 → 2.48+** - 43 minor versions behind, missing features/fixes

### ⚠️ Medium Priority (Performance/Features)
3. **TypeScript 5.5 → 5.8+** - Missing 2-3x faster build times from compile cache
4. **Vite 5 → 6** - Major version available with Environment API
5. **ChromaDB 1.0 → 1.3** - Several improvements and fixes
6. **TipTap 3.0 → 3.10** - Missing modern features like MarkViews

### ✅ Low Priority (Patch updates)
7. **FastAPI 0.118 → 0.121** - Minor updates, no breaking changes
8. **SQLAlchemy 2.0.38 → 2.0.44** - Patch updates
9. **Svelte 5.0 → 5.43** - Patch updates (though project is functional)

---

## Technology Currency Markers Used

Throughout the learning documentation, you'll see these markers:

- ✅ **CURRENT (Nov 2025)** - Pattern/approach is up-to-date
- ⚠️ **OUTDATED PATTERN** - Works but newer approaches exist
- 🚨 **DEPRECATED** - Should not be used in new code
- 🆕 **NEW IN 2025** - Feature/pattern introduced recently
- 🔍 **NEEDS VERIFICATION** - Uncertain, requires investigation
- ❓ **ASSUMPTION** - Inference, not confirmed fact

---

## Key Takeaways for Learning Materials

1. **Svelte 5 Runes are CRITICAL** - This is a paradigm shift from Svelte 3/4
2. **Tailwind v4 is brand new** - Most online tutorials are outdated
3. **LangChain 1.0 is out** - Project uses deprecated v0.3
4. **SQLAlchemy 2.0 patterns** - Completely different from 1.x
5. **Pydantic v2 required** - v1 is deprecated
6. **Python 3.11-3.12 are current** - Well-supported choices

---

**Last Updated:** November 18, 2025
**Next Review:** January 2026 or when major versions change
