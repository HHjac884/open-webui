# Active Context: Open WebUI

## Current Status
- **Memory Bank**: Newly initialized (January 2026)
- **Project Version**: v0.7.1
- **Development Phase**: Active development with regular releases

## Current Focus Areas

### Active Development Priorities
Based on the project's README and structure, the following areas are actively maintained:

1. **Multi-Model Support**
   - Ollama integration
   - OpenAI-compatible API support
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

### Local Development
```bash
# Backend
cd backend
pip install -r requirements.txt
python -m open_webui.main

# Frontend
npm install
npm run dev
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

### Frontend
- `src/lib/stores/` - Global state (Svelte stores)
- `src/lib/apis/` - API client functions
- `src/lib/components/` - Reusable components

## External Integrations

### LLM Providers
- Ollama (local)
- OpenAI
- LMStudio
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

## Next Steps (Suggestions)

When working on this project, consider:
1. Review `systemPatterns.md` for architectural patterns
2. Check `techContext.md` for technology stack details
3. Follow existing router patterns when adding new endpoints
4. Use proper dependencies for authentication/authorization
5. Keep configuration centralized in `config.py`
6. Add migrations for database changes
7. Test with both SQLite and PostgreSQL if applicable
