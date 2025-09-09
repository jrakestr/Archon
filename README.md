<p align="center">
  <img src="./archon-ui-main/public/archon-main-graphic.png" alt="Archon Main Graphic" width="853" height="422">
</p>

<p align="center">
  <em>Power up your AI coding assistants with your own custom knowledge base and task management as an MCP server</em>
</p>

<p align="center">
  <a href="#quick-start">Quick Start</a> •
  <a href="#upgrading">Upgrading</a> •
  <a href="#whats-included">What's Included</a> •
  <a href="#architecture">Architecture</a> •
  <a href="#troubleshooting">Troubleshooting</a>
</p>

---

## 🎯 What is Archon?

> Archon is currently in beta! Expect things to not work 100%, and please feel free to share any feedback and contribute with fixes/new features! Thank you to everyone for all the excitement we have for Archon already, as well as the bug reports, PRs, and discussions. It's a lot for our small team to get through but we're committed to addressing everything and making Archon into the best tool it possibly can be!

Archon is the **command center** for AI coding assistants. For you, it's a sleek interface to manage knowledge, context, and tasks for your projects. For the AI coding assistant(s), it's a **Model Context Protocol (MCP) server** to collaborate on and leverage the same knowledge, context, and tasks. Connect Claude Code, Kiro, Cursor, Windsurf, etc. to give your AI agents access to:

- **Your documentation** (crawled websites, uploaded PDFs/docs)
- **Smart search capabilities** with advanced RAG strategies
- **Task management** integrated with your knowledge base
- **Real-time updates** as you add new content and collaborate with your coding assistant on tasks
- **Much more** coming soon to build Archon into an integrated environment for all context engineering

This new vision for Archon replaces the old one (the agenteer). Archon used to be the AI agent that builds other agents, and now you can use Archon to do that and more.

> It doesn't matter what you're building or if it's a new/existing codebase - Archon's knowledge and task management capabilities will improve the output of **any** AI driven coding.

## 🔗 Important Links

- **[GitHub Discussions](https://github.com/coleam00/Archon/discussions)** - Join the conversation and share ideas about Archon
- **[Contributing Guide](CONTRIBUTING.md)** - How to get involved and contribute to Archon
- **[Introduction Video](https://youtu.be/8pRc_s2VQIo)** - Getting started guide and vision for Archon
- **[Archon Kanban Board](https://github.com/users/coleam00/projects/1)** - Where maintainers are managing issues/features
- **[Dynamous AI Mastery](https://dynamous.ai)** - The birthplace of Archon - come join a vibrant community of other early AI adopters all helping each other transform their careers and businesses!

## Quick Start

### Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- [Node.js 18+](https://nodejs.org/) (for hybrid development mode)
- [Supabase](https://supabase.com/) account (free tier or local Supabase both work)
- [OpenAI API key](https://platform.openai.com/api-keys) (Gemini and Ollama are supported too!)
- (OPTIONAL) [Make](https://www.gnu.org/software/make/) (see [Installing Make](#installing-make) below)

### Setup Instructions

1. **Clone Repository**:
   ```bash
   git clone https://github.com/coleam00/archon.git
   ```
   ```bash
   cd archon
   ```
2. **Environment Configuration**:

   ```bash
   cp .env.example .env
   # Edit .env and add your Supabase credentials:
   # SUPABASE_URL=https://your-project.supabase.co
   # SUPABASE_SERVICE_KEY=your-service-key-here
   ```
   **For ngrok integration** (optional - useful for external AI client access):
   ```bash
   # Install ngrok if not already installed
   # Then expose the MCP server externally
   ngrok http 8051
   ```

   IMPORTANT NOTES:
   - For cloud Supabase: they recently introduced a new type of service role key but use the legacy one (the longer one).
   - For local Supabase: set SUPABASE_URL to http://host.docker.internal:8000 (unless you have an IP address set up).
   - **ngrok integration**: Use `ngrok http 8051` to expose your MCP server for external AI client connections

3. **Database Setup**: In your [Supabase project](https://supabase.com/dashboard) SQL Editor, copy, paste, and execute the contents of `migration/complete_setup.sql`

4. **Start Services** (choose one):

   **Full Docker Mode (Recommended for Normal Archon Usage)**

   ```bash
   docker compose up --build -d
   ```

   **With AI Agents Service (Optional)**

   ```bash
   docker compose --profile agents up --build -d
   ```

   This starts all core microservices in Docker:
   - **Server**: Core API and business logic (Port: 8181)
   - **MCP Server**: Protocol interface for AI clients (Port: 8051)
   - **UI**: Web interface (Port: 3737)
   - **Agents**: AI/ML operations and reranking (Port: 8052) - *only with agents profile*

   All ports are configurable in your `.env` file!

5. **Configure API Keys**:
   - Open http://localhost:3737
   - You'll automatically be brought through an onboarding flow to set your API key (OpenAI is default)

## ⚡ Quick Test

Once everything is running:

1. **Test Web Crawling**: Go to http://localhost:3737 → Knowledge Base → "Crawl Website" → Enter a doc URL (such as https://ai.pydantic.dev/llms-full.txt)
2. **Test Document Upload**: Knowledge Base → Upload a PDF
3. **Test Projects**: Projects → Create a new project and add tasks
4. **Integrate with your AI coding assistant**: MCP Dashboard → Copy connection config for your AI coding assistant
5. **External Access (Optional)**: Use `ngrok http 8051` to expose MCP server for remote AI client connections

## Installing Make

<details>
<summary><strong>🛠️ Make installation (OPTIONAL - For Dev Workflows)</strong></summary>

### Windows

```bash
# Option 1: Using Chocolatey
choco install make

# Option 2: Using Scoop
scoop install make

# Option 3: Using WSL2
wsl --install
# Then in WSL: sudo apt-get install make
```

### macOS

```bash
# Make comes pre-installed on macOS
# If needed: brew install make
```

### Linux

```bash
# Debian/Ubuntu
sudo apt-get install make

# RHEL/CentOS/Fedora
sudo yum install make
```

</details>

<details>
<summary><strong>🚀 Quick Command Reference for Make</strong></summary>
<br/>

| Command           | Description                                             |
| ----------------- | ------------------------------------------------------- |
| `make dev`        | Start hybrid dev (backend in Docker, frontend local) ⭐ |
| `make dev-docker` | Everything in Docker                                    |
| `make stop`       | Stop all services                                       |
| `make test`       | Run all tests                                           |
| `make lint`       | Run linters                                             |
| `make install`    | Install dependencies                                    |
| `make check`      | Check environment setup                                 |
| `make clean`      | Remove containers and volumes (with confirmation)       |

</details>

## 🔄 Database Reset (Start Fresh if Needed)

If you need to completely reset your database and start fresh:

<details>
<summary>⚠️ <strong>Reset Database - This will delete ALL data for Archon!</strong></summary>

1. **Run Reset Script**: In your Supabase SQL Editor, run the contents of `migration/RESET_DB.sql`

   ⚠️ WARNING: This will delete all Archon specific tables and data! Nothing else will be touched in your DB though.

2. **Rebuild Database**: After reset, run `migration/complete_setup.sql` to create all the tables again.

3. **Restart Services**:

   ```bash
   docker compose --profile full up -d
   ```

4. **Reconfigure**:
   - Select your LLM/embedding provider and set the API key again
   - Re-upload any documents or re-crawl websites

The reset script safely removes all tables, functions, triggers, and policies with proper dependency handling.

</details>

## 📚 Documentation

### Core Services

| Service            | Container Name | Default URL           | Purpose                           | Profile Required |
| ------------------ | -------------- | --------------------- | --------------------------------- | ---------------- |
| **Web Interface**  | archon-ui      | http://localhost:3737 | Main dashboard and controls       | Default          |
| **API Service**    | archon-server  | http://localhost:8181 | Web crawling, document processing | Default          |
| **MCP Server**     | archon-mcp     | http://localhost:8051 | Model Context Protocol interface  | Default          |
| **Agents Service** | archon-agents  | http://localhost:8052 | AI/ML operations, reranking       | `agents`         |

### Environment Variables

Your Archon installation uses the following key environment variables (configured in `.env`):

#### Required Configuration
```bash
# Supabase Database Connection (REQUIRED)
SUPABASE_URL=https://your-project.supabase.co
SUPABASE_SERVICE_KEY=your-service-role-key-here

# Service Ports (Optional - defaults shown)
HOST=localhost
ARCHON_SERVER_PORT=8181
ARCHON_MCP_PORT=8051
ARCHON_AGENTS_PORT=8052
ARCHON_UI_PORT=3737
```

#### Optional Configuration
```bash
# Logging and Development
LOG_LEVEL=INFO
LOGFIRE_TOKEN=your-logfire-token

# Frontend Development
VITE_ALLOWED_HOSTS=192.168.1.100,myhost.local
VITE_SHOW_DEVTOOLS=false

# Production Mode (single port proxy)
PROD=false

# Embedding Configuration
EMBEDDING_DIMENSIONS=1536
```

#### API Keys and Model Configuration
API keys and model settings are now managed through the web interface:
- Navigate to Settings → API Configuration
- All keys are encrypted and stored securely in the database
- Supports OpenAI, Ollama, and Google Gemini models

### External Access with ngrok

For remote AI client connections or sharing your Archon instance:

```bash
# Install ngrok (if not already installed)
# macOS: brew install ngrok
# Windows: choco install ngrok
# Linux: snap install ngrok

# Expose MCP server externally
ngrok http 8051

# Use the provided ngrok URL in your AI client configuration
# Example: https://abc123.ngrok.io instead of http://localhost:8051
```

**Use Cases for ngrok:**
- Connect AI clients running on different machines
- Share your Archon instance with team members
- Access Archon from cloud-based AI coding assistants
- Development and testing with external services

## Upgrading

To upgrade Archon to the latest version:

1. **Pull latest changes**:
   ```bash
   git pull
   ```

2. **Check for migrations**: Look in the `migration/` folder for any SQL files newer than your last update. Check the file created dates to determine if you need to run them. You can run these in the SQL editor just like you did when you first set up Archon. We are also working on a way to make handling these migrations automatic!

3. **Rebuild and restart**:
   ```bash
   docker compose up -d --build
   ```

This is the same command used for initial setup - it rebuilds containers with the latest code and restarts services.

## What's Included

### 🧠 Knowledge Management

- **Smart Web Crawling**: Automatically detects and crawls entire documentation sites, sitemaps, and individual pages
- **Document Processing**: Upload and process PDFs, Word docs, markdown files, and text documents with intelligent chunking
- **Code Example Extraction**: Automatically identifies and indexes code examples from documentation for enhanced search
- **Vector Search**: Advanced semantic search with contextual embeddings for precise knowledge retrieval
- **Source Management**: Organize knowledge by source, type, and tags for easy filtering

### 🤖 AI Integration

- **Model Context Protocol (MCP)**: Connect any MCP-compatible client (Claude Code, Cursor, even non-AI coding assistants like Claude Desktop)
- **MCP Tools**: Comprehensive yet simple set of tools for RAG queries, task management, and project operations
- **Multi-LLM Support**: Works with OpenAI, Ollama, and Google Gemini models
- **RAG Strategies**: Hybrid search, contextual embeddings, and result reranking for optimal AI responses
- **Real-time Streaming**: Live responses from AI agents with progress tracking

### 📋 Project & Task Management

- **Hierarchical Projects**: Organize work with projects, features, and tasks in a structured workflow
- **AI-Assisted Creation**: Generate project requirements and tasks using integrated AI agents
- **Document Management**: Version-controlled documents with collaborative editing capabilities
- **Progress Tracking**: Real-time updates and status management across all project activities

### 🔄 Real-time Collaboration

- **WebSocket Updates**: Live progress tracking for crawling, processing, and AI operations
- **Multi-user Support**: Collaborative knowledge building and project management
- **Background Processing**: Asynchronous operations that don't block the user interface
- **Health Monitoring**: Built-in service health checks and automatic reconnection

## Architecture

### Microservices Structure

Archon uses true microservices architecture with clear separation of concerns:

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Frontend UI   │    │  Server (API)   │    │   MCP Server    │    │ Agents Service  │
│                 │    │                 │    │                 │    │                 │
│  React + Vite   │◄──►│    FastAPI +    │◄──►│    Lightweight  │◄──►│   PydanticAI    │
│  Port 3737      │    │    SocketIO     │    │    HTTP Wrapper │    │   Port 8052     │
│                 │    │    Port 8181    │    │    Port 8051    │    │                 │
└─────────────────┘    └─────────────────┘    └─────────────────┘    └─────────────────┘
         │                        │                        │                        │
         └────────────────────────┼────────────────────────┼────────────────────────┘
                                  │                        │
                         ┌─────────────────┐               │
                         │    Database     │               │
                         │                 │               │
                         │    Supabase     │◄──────────────┘
                         │    PostgreSQL   │
                         │    PGVector     │
                         └─────────────────┘
```

### Service Responsibilities

| Service        | Location             | Purpose                      | Key Features                                                       |
| -------------- | -------------------- | ---------------------------- | ------------------------------------------------------------------ |
| **Frontend**   | `archon-ui-main/`    | Web interface and dashboard  | React, TypeScript, TailwindCSS, Socket.IO client                   |
| **Server**     | `python/src/server/` | Core business logic and APIs | FastAPI, service layer, Socket.IO broadcasts, all ML/AI operations |
| **MCP Server** | `python/src/mcp/`    | MCP protocol interface       | Lightweight HTTP wrapper, MCP tools, session management         |
| **Agents**     | `python/src/agents/` | PydanticAI agent hosting     | Document and RAG agents, streaming responses                       |

### Communication Patterns

- **HTTP-based**: All inter-service communication uses HTTP APIs
- **Socket.IO**: Real-time updates from Server to Frontend
- **MCP Protocol**: AI clients connect to MCP Server via SSE or stdio
- **No Direct Imports**: Services are truly independent with no shared code dependencies

### Key Architectural Benefits

- **Lightweight Containers**: Each service contains only required dependencies
- **Independent Scaling**: Services can be scaled independently based on load
- **Development Flexibility**: Teams can work on different services without conflicts
- **Technology Diversity**: Each service uses the best tools for its specific purpose

## 🔧 Configuring Custom Ports & Hostname

This Archon installation supports flexible port and hostname configuration through environment variables.

### Default Port Configuration

```bash
# Default ports used by this installation
ARCHON_UI_PORT=3737      # Web interface
ARCHON_SERVER_PORT=8181  # API service
ARCHON_MCP_PORT=8051     # MCP server (AI client connections)
ARCHON_AGENTS_PORT=8052  # AI agents service
ARCHON_DOCS_PORT=3838    # Documentation (optional)
```

### Changing Ports

Customize ports by updating your `.env` file:

```bash
# Example: Custom port configuration
ARCHON_SERVER_PORT=8282
ARCHON_MCP_PORT=8151
ARCHON_UI_PORT=4000
```

### Hostname Configuration

Configure custom hostname or IP address:

```bash
# Hostname Configuration
HOST=localhost  # Default

# Examples for different deployment scenarios:
HOST=192.168.1.100     # Local network access
HOST=archon.local      # Custom local domain
HOST=myserver.com      # Public domain
HOST=0.0.0.0          # Bind to all interfaces
```

### Network Access Scenarios

**Local Development:**
```bash
HOST=localhost
# Access: http://localhost:3737
```

**Network Sharing:**
```bash
HOST=192.168.1.100
VITE_ALLOWED_HOSTS=192.168.1.100,192.168.1.0/24
# Team members can access: http://192.168.1.100:3737
```

**External Access with ngrok:**
```bash
# Keep default localhost settings
# Use ngrok for external MCP access
ngrok http 8051
# AI clients connect to: https://abc123.ngrok.io
```

### Applying Configuration Changes

After modifying `.env`:

1. **Restart services:**
   ```bash
   docker compose down
   docker compose up -d --build
   ```

2. **Update AI client configurations** with new hostname/ports

3. **Verify connectivity:**
   ```bash
   curl http://${HOST}:${ARCHON_SERVER_PORT}/health
   ```

## 🔧 Development

### Quick Start

```bash
# Install dependencies
make install

# Start development (recommended)
make dev        # Backend in Docker, frontend local with hot reload

# Alternative: Everything in Docker
make dev-docker # All services in Docker

# Stop everything (local FE needs to be stopped manually)
make stop
```

### Development Modes

This Archon installation includes optimized development workflows:

#### Hybrid Mode (Recommended) - `make dev`

Best for active development with instant frontend updates:

- Backend services run in Docker (isolated, consistent)
- Frontend runs locally with hot module replacement
- Instant UI updates without Docker rebuilds
- Automatic environment variable handling

#### Full Docker Mode - `make dev-docker`

For all services in Docker environment:

- All services run in Docker containers
- Better for integration testing
- Consistent across all environments
- Includes optional agents service with `--profile agents`

#### Docker Compose Profiles

This installation uses Docker Compose profiles for flexible service management:

```bash
# Default services only (server, mcp, frontend)
docker compose up -d

# Include AI agents service
docker compose --profile agents up -d

# Development with all services
docker compose --profile full up -d
```

### Testing & Code Quality

```bash
# Run tests
make test       # Run all tests
make test-fe    # Run frontend tests
make test-be    # Run backend tests

# Run linters
make lint       # Lint all code
make lint-fe    # Lint frontend code
make lint-be    # Lint backend code

# Check environment
make check      # Verify environment setup

# Clean up
make clean      # Remove containers and volumes (asks for confirmation)
```

### Viewing Logs

```bash
# View logs using Docker Compose directly
docker compose logs -f              # All services
docker compose logs -f archon-server # API server
docker compose logs -f archon-mcp    # MCP server
docker compose logs -f archon-ui     # Frontend
```

**Note**: The backend services are configured with `--reload` flag in their uvicorn commands and have source code mounted as volumes for automatic hot reloading when you make changes.

## Troubleshooting

### Common Issues and Solutions

#### Port Conflicts

If you see "Port already in use" errors:

```bash
# Check what's using a port (e.g., 3737)
lsof -i :3737

# Stop all containers and local services
make stop

# Change the port in .env
```

#### Docker Permission Issues (Linux)

If you encounter permission errors with Docker:

```bash
# Add your user to the docker group
sudo usermod -aG docker $USER

# Log out and back in, or run
newgrp docker
```

#### Windows-Specific Issues

- **Make not found**: Install Make via Chocolatey, Scoop, or WSL2 (see [Installing Make](#installing-make))
- **Line ending issues**: Configure Git to use LF endings:
  ```bash
  git config --global core.autocrlf false
  ```

#### Frontend Can't Connect to Backend

- Check backend is running: `curl http://localhost:8181/health`
- Verify port configuration in `.env`
- For custom ports, ensure both `ARCHON_SERVER_PORT` and `VITE_ARCHON_SERVER_PORT` are set

#### Docker Compose Hangs

If `docker compose` commands hang:

```bash
# Reset Docker Compose
docker compose down --remove-orphans
docker system prune -f

# Restart Docker Desktop (if applicable)
```

#### Hot Reload Not Working

- **Frontend**: Ensure you're running in hybrid mode (`make dev`) for best HMR experience
- **Backend**: Check that volumes are mounted correctly in `docker-compose.yml`
- **File permissions**: On some systems, mounted volumes may have permission issues

## 📈 Progress

<p align="center">
  <a href="https://star-history.com/#coleam00/Archon&Date">
    <img src="https://api.star-history.com/svg?repos=coleam00/Archon&type=Date" width="500" alt="Star History Chart">
  </a>
</p>

## 📄 License

Archon Community License (ACL) v1.2 - see [LICENSE](LICENSE) file for details.

**TL;DR**: Archon is free, open, and hackable. Run it, fork it, share it - just don't sell it as-a-service without permission.
