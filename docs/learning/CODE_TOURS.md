# Code Tours

**Walk through real features end-to-end.**

**Documented:** November 18, 2025

---

## Tour 1: Chat Message Flow

**User sends message → LLM response**

1. **Frontend:** User types in `MessageInput.svelte`
   - File: [src/lib/components/chat/MessageInput/](../../src/lib/components/chat/MessageInput/)

2. **API Call:** `sendChatMessage()` called
   - File: [src/lib/apis/chats/](../../src/lib/apis/chats/)

3. **Backend Router:** POST `/api/chat/completions`
   - File: [backend/open_webui/routers/openai.py](../../backend/open_webui/routers/openai.py)

4. **LangChain:** Stream from LLM
   - Uses LangChain to call Ollama/OpenAI

5. **Stream Back:** Server-Sent Events to frontend

6. **Update UI:** Tokens appended live in Chat.svelte

---

## Tour 2: RAG Document Query

**User uploads doc → Asks question → Gets answer**

1. **Upload:** `FileUpload` component
   - POST `/api/files/upload`

2. **Process:** Backend extracts text
   - Uses PyPDF2, python-docx, etc.

3. **Embed:** Generate embeddings
   - sentence-transformers

4. **Store:** Save to ChromaDB
   - Vector database

5. **Query:** User asks question
   - Generate query embedding
   - Search ChromaDB for similar chunks

6. **Augment:** Add context to prompt

7. **Generate:** LLM responds with context

---

## Tour 3: Real-time Collaboration

**User joins channel → Sees live updates**

1. **Connect:** WebSocket connection
   - File: [backend/open_webui/socket/main.py](../../backend/open_webui/socket/main.py)

2. **Join:** User joins channel room

3. **Message:** Another user sends message

4. **Broadcast:** Socket.io broadcasts to all in room

5. **Update:** UI updates live for all users

---

**Last updated:** November 18, 2025
