# System Patterns: Open WebUI

## Architecture Overview
Open WebUI follows a **separated backend-frontend architecture** with Python FastAPI for the backend and SvelteKit for the frontend.

## Backend Architecture

### Directory Structure
```
backend/open_webui/
├── main.py              # FastAPI application entry point
├── config.py            # Configuration management
├── constants.py         # Application constants
├── env.py               # Environment variable handling
├── functions.py         # Backend functions
├── tasks.py             # Background task management
├── models/              # SQLAlchemy ORM models
│   ├── auths.py
│   ├── chats.py
│   ├── files.py
│   ├── functions.py
│   ├── models.py
│   ├── users.py
│   └── ...
├── routers/             # FastAPI route handlers
│   ├── auths.py
│   ├── chats.py
│   ├── configs.py
│   ├── models.py
│   ├── ollama.py
│   ├── openai.py
│   └── ...
├── retrieval/           # RAG implementation
│   ├── utils.py
│   ├── loaders/
│   ├── models/
│   └── vector/
├── socket/              # WebSocket handling
├── storage/             # Storage adapters
├── tools/               # Built-in tools
├── utils/               # Utility functions
└── internal/            # Internal utilities (db.py, wrappers.py)
```

### Key Backend Patterns

#### 1. Router Pattern
Each router module defines a FastAPI `APIRouter` with related endpoints:
```python
from fastapi import APIRouter

router = APIRouter()

@router.get("/endpoint")
async def endpoint_handler():
    return {"data": "result"}
```
Included in `main.py` with prefix: `app.include_router(router, prefix="/api/v1/route", tags=["route"])`

#### 2. Dependency Injection Pattern
FastAPI dependencies are used extensively for authentication and authorization:
```python
from fastapi import Depends
from open_webui.utils.auth import get_verified_user, get_admin_user

@app.get("/endpoint")
async def endpoint(user=Depends(get_verified_user)):
    return {"user_id": user.id}
```

#### 3. Model Pattern
SQLAlchemy models use declarative base:
```python
from sqlalchemy import Column, String, Integer
from open_webui.internal.db import Base

class BaseModel(Base):
    __tablename__ = "models"
    id = Column(String, primary_key=True)
    name = Column(String)
```

#### 4. Configuration Pattern
All configuration is centralized in `config.py` and loaded from environment variables:
```python
from pydantic_settings import BaseSettings

class AppConfig(BaseSettings):
    WEBUI_NAME: str = "Open WebUI"
    ENABLE_SIGNUP: bool = True
    # ... more config
    
    class Config:
        env_file = ".env"
```

#### 5. Session Management Pattern
Two session approaches:
- Default: Starlette's SessionMiddleware (cookie-based)
- Redis: StarSessionsMiddleware with RedisStore

#### 6. WebSocket Pattern
Socket.IO handles real-time communication:
```python
from open_webui.socket.main import app as socket_app
app.mount("/ws", socket_app)
```

## Frontend Architecture

### Directory Structure
```
src/
├── routes/              # SvelteKit routes
│   ├── (app)/          # App pages (protected)
│   ├── auth/           # Authentication pages
│   ├── error/          # Error pages
│   ├── s/              # Shared pages
│   └── watch/          # Watch pages
├── lib/
│   ├── apis/           # API client functions
│   ├── components/     # Reusable Svelte components
│   ├── constants/      # Frontend constants
│   ├── i18n/           # Internationalization
│   ├── pyodide/        # Pyodide (Python in browser)
│   ├── stores/         # Svelte stores (state management)
│   ├── types/          # TypeScript type definitions
│   ├── utils/          # Utility functions
│   └── workers/        # Web Workers
├── app.html            # HTML template
├── app.css             # Global styles
└── tailwind.css        # Tailwind imports
```

### Key Frontend Patterns

#### 1. Store Pattern
Svelte stores manage global state:
```typescript
// src/lib/stores/user.ts
import { writable } from 'svelte/store';

export const user = writable<User | null>(null);
export const users = writable<User[]>([]);
```

#### 2. API Client Pattern
API calls are centralized in `src/lib/apis/`:
```typescript
// src/lib/apis/chats.ts
export const getChats = async () => {
    const response = await fetch('/api/v1/chats');
    return await response.json();
};
```

#### 3. Component Pattern
Reusable components follow a consistent structure:
```svelte
<script lang="ts">
  import { onMount } from 'svelte';
  
  export let prop: string;
  // component logic
</script>

<div class="component">
  <!-- template -->
</div>

<style>
  /* scoped styles */
</style>
```

#### 4. Route Pattern
Protected routes use load functions for authentication:
```typescript
// src/routes/(app)/settings/+page.ts
export const load = async ({ fetch, parent }) => {
    const { user } = await parent();
    if (!user) {
        throw redirect(302, '/auth/login');
    }
    return { data };
};
```

## Cross-Cutting Patterns

### 1. Authentication Flow
1. User logs in via `/api/v1/auths/signin`
2. JWT token returned and stored in cookie
3. Subsequent requests include `Authorization: Bearer <token>` header
4. `get_verified_user()` dependency validates token

### 2. Chat Completion Flow
1. Frontend: POST to `/api/v1/chat/completions`
2. Backend: Validate user, model access, permissions
3. Backend: Process payload (RAG, tools, files)
4. Backend: Route to model provider (Ollama/OpenAI)
5. Response: Stream tokens via WebSocket
6. Frontend: Update UI with streaming response
7. Backend: Store message in database

### 3. RAG Processing Flow
1. User attaches files or uses `#` command
2. Document extracted (Tika/Docling/etc.)
3. Text chunked and embedded
4. Vectors stored in configured vector DB
5. Query generates embeddings
6. Vector search retrieves relevant chunks
7. Context injected into chat prompt

### 4. Configuration Loading
1. `main.py` imports from `config.py`
2. `config.py` reads from `.env` via `env.py`
3. Configuration stored in `app.state.config`
4. Router dependencies access via `request.app.state.config`

## Critical Implementation Paths

### User Creation and Authentication
1. `routers/auths.py:signin()` → `utils/auth.py:create_token()` → JWT issued
2. LDAP path: `routers/auths.py:signin_with_ldap()`
3. OAuth path: `main.py:oauth_login_callback()`

### Chat Request Processing
1. `main.py:chat_completion()` → `utils/chat.py:generate_chat_completion()`
2. Model routing: `utils/models.py:get_all_models()` → `get_filtered_models()`

### RAG Processing
1. `routers/retrieval.py` → `retrieval/utils.py` → `retrieval/loaders/` or `retrieval/vector/`

### Model Management
1. `routers/models.py` → `utils/models.py` → Model cache in `app.state.MODELS`

## Middleware Stack (Order Matters)
1. `CompressMiddleware` - Response compression (optional)
2. `RedirectMiddleware` - URL redirects
3. `SecurityHeadersMiddleware` - Security headers
4. `APIKeyRestrictionMiddleware` - API key restrictions
5. Session middleware (Starlette or Redis)
6. `commit_session_after_request` - DB session commit
7. `check_url` - Authorization check
8. `inspect_websocket` - WebSocket validation
9. `CORSMiddleware` - CORS handling

## Database Schema Pattern
- All models inherit from `open_webui.internal.db.Base`
- Use `ScopedSession` for DB sessions
- Session committed after each request via middleware
- Alembic for migrations (in `backend/open_webui/migrations/`)
