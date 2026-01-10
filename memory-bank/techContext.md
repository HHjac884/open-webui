# Tech Context: Open WebUI

## Technology Stack Overview

### Backend (Python)

#### Core Framework
- **FastAPI** - ASGI web framework for building APIs
- **SQLAlchemy** - ORM for database operations
- **Alembic** - Database migration tool
- **Pydantic** - Data validation using Python type annotations

#### Database & Storage
- **SQLite** - Default embedded database (with optional encryption)
- **PostgreSQL** - Alternative production database
- **Redis** - Session storage, caching, task queue (optional but recommended)
- **Cloud Storage** - S3, Google Cloud Storage, Azure Blob Storage support

#### AI/ML Libraries
- **Transformers** (Hugging Face) - Model inference
- **LangChain** - LLM framework integration
- **Sentence-Transformers** - Embedding generation
- **ChromaDB**, **Qdrant**, **PGVector** - Vector databases for RAG
- **FAISS** - Vector similarity search
- **Pyodide** - Python in browser for code execution

#### Web & Networking
- **aiohttp** - Async HTTP client
- **requests** - Sync HTTP client
- **socket.io** (Python) - WebSocket support
- **python-multipart** - File upload handling
- **python-jose** - JWT token handling

#### Authentication & Security
- **passlib** - Password hashing
- **bcrypt** - Password hashing algorithm
- **python-jose** - JWT encoding/decoding
- **python-ldap** - LDAP/Active Directory integration

#### Content Processing
- **PyPDF2**, **pypdf** - PDF processing
- **python-docx** - Word document processing
- **python-pptx** - PowerPoint processing
- **Pillow** - Image processing
- **beautifulsoup4** - HTML parsing
- **markdown** - Markdown rendering

#### Audio Processing
- **faster-whisper** - Speech-to-Text
- **openai** - OpenAI API client
- **azure-cognitiveservices-speech** - Azure Speech SDK

#### Utilities
- **python-dotenv** - Environment variable loading
- **pydantic-settings** - Configuration management
- **tenacity** - Retry logic
- **aiocache** - Async caching
- **uvicorn** - ASGI server

### Frontend (TypeScript/Svelte)

#### Core Framework
- **Svelte 5** - Reactive UI framework
- **SvelteKit** - Full-stack Svelte application framework
- **TypeScript** - Type-safe JavaScript

#### Build Tools
- **Vite** - Build tool and dev server
- **PostCSS** - CSS transformation
- **Tailwind CSS 4** - Utility-first CSS framework

#### UI Components
- **bits-ui** - Headless UI components
- **svelte-sonner** - Toast notifications
- **@sveltejs/svelte-virtual-list** - Virtual scrolling
- **paneforge** - Split panes

#### Rich Text Editing
- **@tiptap/core** - ProseMirror-based editor
- **@tiptap/starter-kit** - Base Tiptap extensions
- **prosemirror** - Rich text editor framework
- **yjs** - Real-time collaboration framework

#### Code Editing
- **codemirror** - Code editor
- **@codemirror/lang-javascript** - JavaScript language support
- **@codemirror/lang-python** - Python language support
- **highlight.js** - Syntax highlighting

#### Visualization
- **chart.js** - Charting library
- **mermaid** - Diagram rendering
- **vega**, **vega-lite** - Declarative visualization

#### PDF & Documents
- **pdfjs-dist** - PDF rendering
- **jspdf** - PDF generation
- **mammoth** - DOCX to HTML conversion

#### Markdown & Math
- **marked** - Markdown parsing
- **katex** - LaTeX rendering
- **turndown** - HTML to Markdown

#### Forms & Interactions
- **@floating-ui/dom** - Popover positioning
- **tippy.js** - Tooltip/popover library
- **alpinejs** - Lightweight JavaScript framework
- **sortablejs** - Drag-and-drop sorting
- **panzoom** - Pan and zoom interactions

#### Internationalization
- **i18next** - Internationalization framework
- **i18next-browser-languagedetector** - Language detection

#### Storage & State
- **idb** - IndexedDB wrapper
- **fuse.js** - Fuzzy search

#### HTTP & Networking
- **undici** - HTTP/1.1 client
- **socket.io-client** - WebSocket client
- **eventsource-parser** - Server-sent events parsing

#### Utilities
- **dayjs** - Date/time manipulation
- **uuid** - UUID generation
- **dompurify** - XSS sanitization
- **html2canvas-pro** - HTML to canvas
- **file-saver** - File download

#### Audio/Video
- **@mediapipe/tasks-vision** - MediaPipe vision tasks
- **kokoro-js** - Text-to-speech

#### Python in Browser
- **@pyscript/core** - Python execution in browser
- **pyodide** - Python runtime for WebAssembly

#### Office Documents
- **xlsx** - Excel file handling
- **heic2any** - HEIC image conversion

## Development Setup

### Backend Development
```bash
# Python version: 3.11+
pip install -r backend/requirements.txt
cd backend
python -m open_webui.main
```

### Frontend Development
```bash
# Node version: 18.13.0 - 22.x.x
npm install
npm run dev  # Runs on http://localhost:5173
```

### Docker Development
```bash
# Main image
docker run -d -p 3000:8080 -v open-webui:/app/backend/data ghcr.io/open-webui/open-webui:main

# With Ollama bundled
docker run -d -p 3000:8080 -v ollama:/root/.ollama -v open-webui:/app/backend/data ghcr.io/open-webui/open-webui:ollama

# With CUDA support
docker run -d -p 3000:8080 --gpus all -v open-webui:/app/backend/data ghcr.io/open-webui/open-webui:cuda
```

## Build & Production

### Frontend Build
```bash
npm run build  # Produces static files in .svelte-kit/output
```

### Production Server
```bash
# Using uvicorn
uvicorn open_webui.main:app --host 0.0.0.0 --port 8080

# Using pip installation
pip install open-webui
open-webui serve
```

## Testing

### Frontend Tests
```bash
npm run test:frontend  # Vitest
npm run cy:open        # Cypress E2E tests
```

### Linting & Formatting
```bash
# Frontend
npm run lint       # ESLint
npm run format     # Prettier

# Backend
npm run lint:backend      # Pylint
npm run format:backend    # Black
```

## Environment Configuration

### Key Environment Variables
See `.env.example` for complete list. Key categories:
- **Database**: `DATABASE_URL`
- **Redis**: `REDIS_URL`, `REDIS_CLUSTER`
- **WebUI**: `WEBUI_NAME`, `WEBUI_AUTH`, `ENABLE_SIGNUP`
- **Ollama**: `OLLAMA_BASE_URLS`
- **OpenAI**: `OPENAI_API_KEY`, `OPENAI_API_BASE_URLS`
- **RAG**: `RAG_EMBEDDING_ENGINE`, `RAG_EMBEDDING_MODEL`
- **LDAP**: `ENABLE_LDAP`, `LDAP_SERVER_HOST`
- **Storage**: `UPLOAD_DIR`, cloud storage credentials

## API Endpoints Structure

### Base URL
- `/api/v1/` - Main API prefix
- `/ollama/` - Ollama-specific endpoints
- `/openai/` - OpenAI-compatible endpoints

### Authentication
- `POST /api/v1/auths/signin` - Sign in
- `POST /api/v1/auths/signout` - Sign out
- `POST /api/v1/auths/signup` - Sign up
- `GET /api/v1/auths/me` - Get current user

### Chat & Models
- `POST /api/v1/chat/completions` - Chat completions (OpenAI-compatible)
- `GET /api/v1/models` - List models
- `GET /api/v1/models/base` - List base models

### RAG & Retrieval
- `POST /api/v1/retrieval/query` - Query documents
- `POST /api/v1/knowledge` - Manage knowledge base
- `POST /api/v1/files` - Upload files

### Configuration
- `GET /api/v1/configs` - Get config
- `POST /api/v1/configs` - Update config (admin)

## Database Migrations

### Create Migration
```bash
cd backend
alembic revision --autogenerate -m "description"
```

### Apply Migrations
```bash
alembic upgrade head
```

## Deployment Considerations

### Horizontal Scaling
- Use Redis for session storage
- Configure `ENABLE_WEBSOCKET_SUPPORT=true`
- Use load balancer with sticky sessions for WebSocket

### Performance
- Enable compression middleware
- Configure thread pool size: `THREAD_POOL_SIZE`
- Use PostgreSQL for high-traffic deployments
- Enable async embedding: `ENABLE_ASYNC_EMBEDDING=true`

### Security
- Set strong `WEBUI_SECRET_KEY`
- Enable HTTPS in production
- Configure CORS: `CORS_ALLOW_ORIGIN`
- Use LDAP/AD for enterprise authentication
- Enable audit logging: `AUDIT_LOG_LEVEL`

### Observability
- Enable OpenTelemetry: `ENABLE_OTEL=true`
- Configure audit logging paths
- Monitor Redis health
- Track model usage via `/api/usage` endpoint
