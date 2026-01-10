# Product Context: Open WebUI

## Problem Statement
Organizations and individuals need a self-hosted, privacy-focused AI platform that:
- Works offline without data leaving the local environment
- Supports multiple AI model providers and custom models
- Provides enterprise-grade features for team collaboration
- Offers flexibility for custom integrations and extensions
- Maintains data sovereignty and security

## Target Users
1. **Privacy-Conscious Individuals**: Users who want AI interactions without data leaving their local environment
2. **Small to Medium Businesses**: Teams needing shared AI tools with access control
3. **Enterprise Organizations**: Companies requiring LDAP/AD integration, SSO, and RBAC
4. **Developers & Researchers**: Users who need to test and deploy custom AI models
5. **Government/Compliance-Focused**: Entities requiring complete data control and audit trails

## User Experience Goals
- **Effortless Setup**: Install with a single Docker command
- **Responsive Design**: Seamless experience across desktop, tablet, and mobile
- **Progressive Web App**: Native app-like experience on mobile devices
- **Intuitive Interface**: Clean, modern UI for chat interactions
- **Real-time Collaboration**: WebSocket support for multi-user environments

## Key Features

### Core AI Capabilities
- Chat completions with streaming support
- Multiple simultaneous model conversations
- Native Python function calling with code editor
- Model builder for custom character/agent creation
- Image generation and editing (DALL-E, Gemini, ComfyUI, AUTOMATIC1111)

### RAG & Document Processing
- Local RAG with 9 vector database options
- Web search integration (15+ providers)
- Document library and file management
- Content extraction from PDFs, docs, images
- Hybrid search (BM25 + vector similarity)

### Communication
- Voice calls (Speech-to-Text, Text-to-Speech)
- Video call support
- Multi-provider STT/TTS engines

### Enterprise Features
- Role-Based Access Control (RBAC)
- LDAP/Active Directory integration
- OAuth 2.0/SSO support
- SCIM 2.0 automated provisioning
- User groups and permissions
- Audit logging

### Developer/Extensibility
- Pipelines plugin framework
- OpenAI-compatible API endpoints
- Webhooks for custom integrations
- API key management with endpoint restrictions
- OpenTelemetry observability

## How It Works

### Architecture
```
┌─────────────────────────────────────────────────────────────┐
│                         Frontend                             │
│                    (SvelteKit + TypeScript)                 │
└────────────────────────┬────────────────────────────────────┘
                         │ HTTP/WebSocket
                         │
┌────────────────────────▼────────────────────────────────────┐
│                        Backend                              │
│                     (Python FastAPI)                        │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐     │
│  │   Routers    │  │   Utils      │  │   Models     │     │
│  └──────────────┘  └──────────────┘  └──────────────┘     │
└────────────────────────┬────────────────────────────────────┘
                         │
         ┌───────────────┼───────────────┐
         │               │               │
    ┌────▼────┐   ┌─────▼─────┐   ┌────▼────┐
    │  SQLite │   │   Redis   │   │ Ollama  │
    │/Postgres│   │ (Session) │   │/OpenAI  │
    └─────────┘   └───────────┘   └─────────┘
```

### Request Flow
1. User sends chat request via Svelte frontend
2. Frontend calls `/api/v1/chat/completions` endpoint
3. Backend authenticates user, validates permissions
4. Request routed to appropriate model provider
5. Streaming response sent via WebSocket
6. Response stored in database (unless local mode)
7. Frontend updates UI with real-time tokens

## Data Flow
- **User Data**: Stored in SQLite/PostgreSQL (optional encryption)
- **Sessions**: Redis-backed for horizontal scalability
- **Files**: Local filesystem or cloud storage (S3, GCS, Azure)
- **Vector Store**: Configurable (ChromaDB, PGVector, Qdrant, etc.)
- **Logs**: File-based or OpenTelemetry integration
