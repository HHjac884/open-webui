# Progress: Open WebUI

## What Works

### Core Functionality
- ✅ User authentication (JWT-based, LDAP, OAuth)
- ✅ User management (create, update, delete users)
- ✅ Chat completions with streaming
- ✅ Multiple model support (Ollama, OpenAI, custom)
- ✅ Real-time responses via WebSocket
- ✅ Chat history and message storage

### RAG & Document Processing
- ✅ Document upload and processing
- ✅ Multiple content extraction engines (Tika, Docling, Document Intelligence, Mistral OCR)
- ✅ Vector embedding generation
- ✅ 9 vector database options (ChromaDB, PGVector, Qdrant, etc.)
- ✅ Web search integration (15+ providers)
- ✅ Hybrid search (BM25 + vector)
- ✅ RAG query and retrieval

### Enterprise Features
- ✅ Role-Based Access Control (RBAC)
- ✅ User groups management
- ✅ LDAP/Active Directory integration
- ✅ OAuth 2.0/SSO support
- ✅ SCIM 2.0 automated provisioning
- ✅ API key management with restrictions
- ✅ Audit logging

### Communication
- ✅ Voice calls (Speech-to-Text, Text-to-Speech)
- ✅ Video call support
- ✅ Multiple STT engines (Whisper, OpenAI, Deepgram, Azure)
- ✅ Multiple TTS engines (Azure, ElevenLabs, OpenAI, WebAPI)

### File Management
- ✅ File upload and storage
- ✅ File metadata handling
- ✅ Image compression
- ✅ Multiple cloud storage backends (S3, GCS, Azure)
- ✅ File type restrictions and size limits

### Developer Features
- ✅ OpenAI-compatible API endpoints
- ✅ Function calling support
- ✅ Model builder for custom models
- ✅ Pipelines plugin framework
- ✅ Tool server connections
- ✅ Webhooks
- ✅ OpenTelemetry support

### UI/UX
- ✅ Responsive design (desktop, tablet, mobile)
- ✅ Progressive Web App support
- ✅ Rich text editor (Tiptap/ProseMirror)
- ✅ Code editor with syntax highlighting
- ✅ Markdown and LaTeX support
- ✅ Dark/light theme support
- ✅ Internationalization (i18n)

## Development Setup (Verified January 2026)

### Frontend Setup
- ✅ npm install with `--force` flag resolves Tiptap dependency conflicts
- ✅ Development server runs on port 5173
- ✅ Production build creates `build/` directory
- ✅ Hot reload enabled via Vite

### Backend Setup
- ✅ Python 3.11+ with Conda environment
- ✅ Dependencies install via `pip install -r requirements.txt`
- ✅ Server runs on port 8080 with uvicorn
- ✅ Backend must run from `backend/` directory

### Known Setup Issues (Documented)

1. **Backend Not Running**
   - **Error**: "Open WebUI Backend Required" in browser
   - **Fix**: Start backend from `backend/` directory
   - **Required**: Both frontend (5173) and backend (8080) must run simultaneously

2. **Module Not Found**
   - **Error**: `ModuleNotFoundError: No module named 'open_webui'`
   - **Cause**: Running backend from wrong directory
   - **Fix**: Navigate to `backend/` before running `python -m uvicorn open_webui.main:app`

3. **npm Install Conflicts**
   - **Error**: `ERESOLVE could not resolve` - Tiptap peer dependency conflicts
   - **Fix**: Use `npm install --force` (documented in official docs)
   - **Alternative**: `npm install --legacy-peer-deps`

4. **Build Directory Missing**
   - **Warning**: `Frontend build directory not found`
   - **Fix**: Run `npm run build` from project root
   - **Note**: Backend serves production frontend from `build/` directory

5. **Pyodide Package Failures**
   - **Error**: Some Pyodide packages (seaborn) fail to fetch from CDN
   - **Impact**: Non-fatal; Pyodide is optional for LM Studio setups
   - **Resolution**: Ignore for LM Studio deployments

## What's Left to Build

### Potential Enhancements (Not Confirmed)
- ⏳ Additional vector database integrations
- ⏳ More content extraction engines
- ⏳ Additional web search providers
- ⏳ Enhanced analytics dashboard
- ⏳ More visualization options

### Feature Requests (Speculative)
- ⏳ Real-time collaboration (yjs is integrated)
- ⏳ Advanced scheduling/automation
- ⏳ Enhanced audit log viewer
- ⏳ Custom branding for enterprise

## Current Status

### Version
- **Current**: v0.7.1
- **Git Commit**: 00b900829a33cc68cbc2d937a94910c173fb15c8
- **Status**: Stable, active development
- **Last Updated**: January 10, 2026

### Known Issues (Updated)

1. **Ollama Connection Issues**
   - Docker container may not reach Ollama at `127.0.0.1:11434`
   - Solution: Use `--network=host` flag or `host.docker.internal`

2. **Frontend Build Directory**
   - Warning logged if `FRONTEND_BUILD_DIR` not found
   - Falls back to API-only mode
   - Solution: Run `npm run build` before starting backend

3. **Dev Branch Instability**
   - `:dev` tag contains bleeding-edge features
   - May have bugs or incomplete features

4. **Backend Dependency Conflicts**
   - Tiptap packages have version mismatches between v2 and v3
   - Documented solution: `npm install --force`

5. **Directory-Sensitive Backend Startup**
   - Backend must be started from `backend/` directory
   - Running from project root causes `ModuleNotFoundError`

### Testing Status
- ✅ Frontend unit tests (Vitest) - configured
- ✅ E2E tests (Cypress) - configured
- ⏳ Backend test coverage - needs verification
- ⏳ Integration tests - limited coverage

## Evolution of Project Decisions

### Architecture Decisions
1. **Separated Backend/Frontend**: Chose FastAPI + SvelteKit for flexibility and independent development
2. **SQLite Default**: Embedded database for easy setup, PostgreSQL option for production
3. **WebSocket Streaming**: Real-time token delivery via Socket.IO for responsive UI
4. **Multiple Vector DBs**: Support for 9 different vector databases for flexibility
5. **Plugin Framework**: Pipelines for extensibility without core modifications

### Technology Choices
1. **FastAPI**: Modern async Python framework with automatic API docs
2. **Svelte 5**: Reactive framework with compile-time optimizations
3. **Tailwind CSS 4**: Latest utility-first CSS framework
4. **SQLAlchemy**: Mature Python ORM with good migration support
5. **Redis**: Recommended for session storage and horizontal scaling

### API Design
1. **OpenAI-Compatible**: Makes migration from OpenAI tools seamless
2. **RESTful Endpoints**: Standard REST patterns for CRUD operations
3. **WebSocket Additions**: Real-time features complement REST API
4. **Authentication**: JWT tokens + session cookies for web UI

## Deployment Notes

### Recommended Deployment
- **Production**: Docker with PostgreSQL and Redis
- **Small Scale**: Docker with SQLite (default)
- **Development**: Native Python + npm (separate processes)
- **Enterprise**: Kubernetes with load balancer and sticky sessions

### Critical Configuration
- `WEBUI_SECRET_KEY` - Must be strong and unique
- `REDIS_URL` - Required for horizontal scaling
- `DATABASE_URL` - PostgreSQL for production
- `CORS_ALLOW_ORIGIN` - Security requirement
- File storage volume - Data persistence

### Development Configuration (Windows)
- Python 3.11+ with Conda environment
- Node.js 22.10+ with `--force` for npm install
- Separate terminals for frontend (5173) and backend (8080)
- Backend must be run from `backend/` directory

## Maintenance Recommendations

### Regular Tasks
1. **Security Updates**: Keep dependencies updated
2. **Database Migrations**: Run Alembic migrations on upgrades
3. **Backup**: Regular backups of `backend/data` directory
4. **Monitoring**: Use OpenTelemetry for observability
5. **Log Rotation**: Configure log rotation for production

### Code Quality
1. **Linting**: Run `npm run lint` and `npm run lint:backend` regularly
2. **Formatting**: Use Prettier (frontend) and Black (backend)
3. **Type Checking**: Run `npm run check` for TypeScript
4. **Testing**: Add tests for new features

## Next Steps for Development

### When Adding Features
1. Follow existing patterns in `systemPatterns.md`
2. Add database migrations if schema changes needed
3. Update `activeContext.md` with new patterns
4. Consider both SQLite and PostgreSQL compatibility
5. Test with Docker deployment
6. Update documentation if user-facing

### When Fixing Bugs
1. Identify root cause in relevant router/model
2. Add tests to prevent regression
3. Update error messages in `constants.py` if needed
4. Document the fix in `activeContext.md` if significant

### When Setting Up Development Environment
1. Navigate to `backend/` directory before starting backend
2. Use `npm install --force` for Tiptap dependency conflicts
3. Run `npm run build` before accessing via backend port
4. Start both frontend (5173) and backend (8080) simultaneously
5. Configure `.env` with `OPENAI_API_BASE_URL` for LM Studio integration

### When Contributing
1. Follow project's contribution guidelines
2. Maintain consistency with existing code style
3. Add appropriate tests
4. Update CHANGELOG if user-facing changes
5. Ensure all linting passes

## Resources

### Documentation
- Official Docs: https://docs.openwebui.com/
- GitHub: https://github.com/open-webui/open-webui
- Discord: https://discord.gg/5rJgQTnV4s

### Files to Update When Making Changes
1. **Feature addition**: Update `activeContext.md` with new patterns
2. **Tech stack change**: Update `techContext.md`
3. **Architecture change**: Update `systemPatterns.md`
4. **Major feature**: Update `productContext.md`
5. **Scope change**: Update `projectbrief.md`
6. **Progress**: Always update `progress.md` after significant work

---

*This progress document was updated on January 10, 2026 to reflect learnings from development setup session.*
