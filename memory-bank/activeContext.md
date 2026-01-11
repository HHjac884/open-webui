# Active Context: Open WebUI

## Current Status
- **Memory Bank**: Newly initialized (January 2026)
- **Project Version**: v0.7.1
- **Development Phase**: Active development with regular releases
- **Last Updated**: January 10, 2026

## Current Focus Areas

### Active Development Priorities
Based on the project's README and structure, the following areas are actively maintained:

1. **Multi-Model Support**
   - Ollama integration
   - OpenAI-compatible API support
   - LM Studio integration (LAN deployment)
   - Custom model creation and management

2. **RAG & Retrieval**
   - 9 vector database options
   - Web search integration (15+ providers)
   - Document processing and extraction

3. **Enterprise Features**
   - RBAC and user permissions
   - LDAP/AD integration
   - SCIM 2.0 provisioning
   - OAuth 2.0/SSO

4. **Extensibility**
   - Pipelines plugin framework
   - Tool server connections
   - Function calling

## Important Patterns & Preferences

### Code Organization
- **Backend routers**: Each feature has a dedicated router in `backend/open_webui/routers/`
- **Frontend routes**: Organized by function with `(app)` for protected routes
- **API consistency**: Use OpenAI-compatible endpoints where possible
- **Error handling**: Standardized via `ERROR_MESSAGES` constants

### Configuration Management
- All configuration in `backend/open_webui/config.py`
- Environment variables via `.env` file
- Configuration stored in `app.state.config`
- Hot-reload for some config options via admin endpoints

### Authentication Flow
- JWT tokens via `Authorization` header
- Session cookies for web UI
- OAuth and LDAP for enterprise auth
- API key authentication for external access

### Database Access
- Use `ScopedSession` for DB sessions
- Sessions auto-commit via middleware
- Alembic for migrations
- Models defined in `backend/open_webui/models/`

### WebSocket Communication
- Socket.IO for real-time updates
- Mounted at `/ws` path
- Used for streaming responses and real-time events
- Redis-backed sessions for horizontal scaling

## Recent Changes

### Version 0.7.1 (Current)
The latest release includes:
- Svelte 5 migration
- Tailwind CSS 4 upgrade
- Enhanced RAG capabilities
- Multiple vector database options
- Enterprise authentication features

## Known Considerations

### Development Setup Issues

#### Backend is Required for Frontend
- **Issue**: Frontend cannot run standalone; backend must be running
- **Error Message**: "Open WebUI Backend Required - Oops! You're using an unsupported method (frontend only)"
- **Reason**: Frontend relies on backend for authentication, database, session management, and API routing
- **Solution**: Both frontend (port 5173) and backend (port 8080) must run simultaneously

#### Frontend Build Required
- **Issue**: Backend serves production frontend from `build/` directory
- **Error**: `WARNI [open_webui.main] Frontend build directory not found at 'C:\__My_Code_Here__\open-webui\build'. Serving API only.`
- **Solution**: Run `npm run build` before production deployment
- **Build Directory**: Defaults to `BASE_DIR / "build"` (configurable via `FRONTEND_BUILD_DIR` env var)

#### Dependency Conflicts (npm)
- **Issue**: Tiptap package version mismatches (v2 vs v3)
- **Error**: `ERESOLVE could not resolve` - peer dependency conflicts
- **Solution**: Use `npm install --force` to override peer dependency conflicts
- **Alternatives**: `npm install --legacy-peer-deps` (less aggressive)
- **Note**: Project docs explicitly recommend `--force` for this situation

#### Pyodide Installation
- **Issue**: Some Pyodide packages (seaborn) may fail to download from CDN
- **Error**: `fetch failed` during `micropip` installation
- **Impact**: Non-fatal - Pyodide is for browser-based Python execution, not required for all use cases
- **For LM Studio Users**: Pyodide not needed when using LM Studio as backend

#### Backend Startup Directory
- **Issue**: Running `python -m uvicorn open_webui.main:app` from wrong directory fails
- **Error**: `ModuleNotFoundError: No module named 'open_webui'`
- **Cause**: Command run from project root instead of `backend/` directory
- **Solution**: Always navigate to `backend/` directory before starting backend

### Performance
- Streaming responses via WebSocket for real-time token delivery
- Async embedding for RAG to reduce latency
- Thread pool size configurable via `THREAD_POOL_SIZE`
- Redis recommended for production deployments

### Security
- All admin endpoints protected by `get_admin_user()` dependency
- API keys can have endpoint restrictions
- LDAP/AD integration for enterprise auth
- Audit logging available via middleware

### Compatibility
- Python 3.11+ required
- Node.js 18.13.0 to 22.x.x for frontend
- Docker recommended for production
- Multiple deployment options (Docker, Kubernetes, native)

## Development Workflow

### Local Development (Windows with Conda)

#### Prerequisites
- Python 3.11+
- Node.js 22.10+
- Conda (recommended) or venv
- Git Bash (for sh commands) or use direct Python commands

#### Frontend Setup
```bash
# From project root
npm install --force  # Use --force due to Tiptap dependency conflicts
npm run dev         # Runs on http://localhost:5173
npm run build       # Build production assets (required for backend serving)
```

#### Backend Setup
```bash
# From project root
conda create --name open-webui python=3.11
conda activate open-webui
cd backend
pip install -r requirements.txt -U
python -m uvicorn open_webui.main:app --port 8080 --host 0.0.0.0 --reload
```

#### Environment Variables (.env)
```bash
# LM Studio Configuration (example for LAN deployment)
OPENAI_API_BASE_URL='http://192.168.x.x:1234'
OPENAI_API_KEY=''
CORS_ALLOW_ORIGIN='*'

# Frontend Build Directory (if different from default)
FRONTEND_BUILD_DIR=C:\__My_Code_Here__\open-webui\build
```

### Testing
- Frontend: Vitest for unit tests, Cypress for E2E
- Backend: Test files in `backend/open_webui/test/`
- Linting: ESLint (frontend), Pylint (backend)

### Deployment
- Docker images: `ghcr.io/open-webui/open-webui`
- Tags: `:main`, `:cuda`, `:ollama`, `:dev`
- Volume required: `-v open-webui:/app/backend/data`

## Key Files to Reference

### Entry Points
- `backend/open_webui/main.py` - Backend FastAPI app
- `src/routes/+layout.svelte` - Frontend root layout
- `package.json` - Frontend dependencies and scripts

### Configuration
- `backend/open_webui/config.py` - All config options
- `backend/open_webui/env.py` - Environment variable handling
- `.env.example` - Environment variable template
- `backend/requirements.txt` - Python dependencies

### Database
- `backend/open_webui/internal/db.py` - DB connection and session
- `backend/open_webui/models/` - SQLAlchemy models
- `backend/open_webui/migrations/` - Alembic migrations

### API Routes
- `backend/open_webui/routers/chats.py` - Chat endpoints
- `backend/open_webui/routers/users.py` - User management
- `backend/open_webui/routers/models.py` - Model management
- `backend/open_webui/routers/auths.py` - Authentication
- `backend/open_webui/routers/openai.py` - OpenAI-compatible endpoints

### Frontend
- `src/lib/stores/` - Global state (Svelte stores)
- `src/lib/apis/` - API client functions
- `src/lib/components/` - Reusable components

## External Integrations

### LLM Providers
- Ollama (local)
- OpenAI
- LM Studio
- GroqCloud
- Mistral
- OpenRouter

### Vector Databases
- ChromaDB
- PGVector
- Qdrant
- Milvus
- Elasticsearch
- OpenSearch
- Pinecone
- S3Vector
- Oracle 23ai

### Web Search Providers
- Google PSE
- SearXNG
- Brave Search
- Kagi
- Mojeek
- Tavily
- Perplexity
- DuckDuckGo
- Bing
- And more...

## Learnings from Recent Session (January 2026)

### LM Studio Connection Issues

#### "Unexpected endpoint or method" Error
- **Error Message**: "Unexpected endpoint or method. (POST /chat/completions). Returning 200 anyway"
- **Source**: LM Studio Developer Logs
- **Root Cause**: Open WebUI makes requests to endpoints that LM Studio doesn't fully support
- **Impact**: 
  - Main chat request: Works (returns `{'status': True}`)
  - Background tasks fail: `generate_follow_ups`, `generate_title`, `generate_chat_tags`
- **Observed Behavior**:
  ```
  INFO  | open_webui.utils.chat:generate_direct_chat_completion:109 - res: {'status': True}
  ERROR | Exception: Unexpected endpoint or method. (POST /chat/completions)
  ```
- **Note**: Silly Tavern works fine with the same LM Studio setup, indicating the issue is specific to Open WebUI's request format

#### Verification Steps
1. Test LM Studio connectivity with curl:
   ```bash
   curl -X POST http://<IP>:1234/v1/chat/completions \
     -H "Content-Type: application/json" \
     -d '{"model": "<model-name>", "messages": [{"role": "user", "content": "Hello"}]}'
   ```
2. If curl works but Open WebUI doesn't, the issue is in Open WebUI's request formatting
3. Check backend logs for exact request format being sent

### Common Setup Pitfalls

1. **Backend Not Running**: Frontend will show "Backend Required" error
   - **Fix**: Start backend in separate terminal from `backend/` directory

2. **Wrong Directory for Backend**: `ModuleNotFoundError: No module named 'open_webui'`
   - **Fix**: Navigate to `backend/` directory before running uvicorn

3. **npm install Fails**: Tiptap peer dependency conflicts
   - **Fix**: Use `npm install --force` (documented solution)

4. **Pyodide Download Errors**: Some packages fail to fetch from CDN
   - **Impact**: Non-fatal; Pyodide is optional for LM Studio setup

5. **Build Directory Missing**: Backend warns about missing `build/` directory
   - **Fix**: Run `npm run build` to create production assets

### LM Studio LAN Deployment

For connecting Open WebUI frontend to LM Studio on a separate LAN computer:

1. **LM Studio Setup** (Computer B):
   - Configure LM Studio to listen on `0.0.0.0:1234` (not localhost)
   - Note LAN IP address (e.g., `192.168.0.50`)

2. **Open WebUI Configuration** (Computer A):
   ```
   OPENAI_API_BASE_URL='http://192.168.0.50:1234'
   OPENAI_API_KEY=''
   CORS_ALLOW_ORIGIN='*'
   ```

3. **CORS Considerations**:
   - Set `CORS_ALLOW_ORIGIN` to allow requests from frontend origin
   - Use wildcard `'*'` for development testing
   - For production, specify exact origins separated by `;`

### Virtual Environment Best Practices

- **Conda Recommended**: Better for AI/ML projects, easier dependency management
- **Environment Isolation**: Each project should have its own conda environment
- **Python Version**: Use Python 3.11 as specified in docs
- **Activation**: Always activate environment (`conda activate open-webui`) before running backend commands

## Next Steps (Suggestions)

When working on this project, consider:
1. Review `systemPatterns.md` for architectural patterns
2. Check `techContext.md` for technology stack details
3. Follow existing router patterns when adding new endpoints
4. Use proper dependencies for authentication/authorization
5. Keep configuration centralized in `config.py`
6. Add migrations for database changes
7. Test with both SQLite and PostgreSQL if applicable
8. When setting up locally, run backend from `backend/` directory
9. Use `npm install --force` to resolve Tiptap dependency conflicts
10. Run `npm run build` before accessing via backend port (8080)
