# Project Brief: Open WebUI

## Project Name
Open WebUI - Self-Hosted AI Platform

## Core Purpose
Open WebUI is an extensible, feature-rich, and user-friendly self-hosted AI platform designed to operate entirely offline. It provides a web interface for interacting with various LLM (Large Language Model) runners, including Ollama and OpenAI-compatible APIs.

## Current Version
v0.7.1

## Primary Goals
1. **Self-Hosted AI Interface**: Provide a comprehensive web UI for AI model interactions
2. **Multi-Model Support**: Support multiple LLM providers (Ollama, OpenAI, Mistral, GroqCloud, etc.)
3. **RAG Integration**: Built-in Retrieval Augmented Generation for document-based queries
4. **Extensibility**: Plugin framework (Pipelines) for custom functionality
5. **Enterprise Features**: RBAC, LDAP/AD integration, SSO, SCIM 2.0 provisioning
6. **Privacy First**: Run entirely offline with local data storage

## Project Type
Full-Stack Web Application
- **Backend**: Python FastAPI
- **Frontend**: Svelte (SvelteKit) with TypeScript
- **Database**: SQLite (default) with PostgreSQL support
- **Cache/Session**: Redis (optional but recommended for production)

## Deployment Methods
- Docker (recommended)
- Docker Compose
- Kubernetes (kubectl, kustomize, helm)
- Native Python installation via pip

## Licensing
Open WebUI License with additional branding requirements. Contains components under multiple licenses - see LICENSE_HISTORY for details.

## Repository
https://github.com/open-webui/open-webui
