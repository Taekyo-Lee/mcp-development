# MCP Servers - Model Context Protocol Server Collection

A comprehensive collection of MCP (Model Context Protocol) servers running in Docker containers, providing AI agents with powerful tool-calling capabilities.

## 🚀 100% Self-Contained & Portable

This project is **completely independent** and can be:
- ✅ Copied or moved to any location
- ✅ Deployed as a standalone service
- ✅ Moved to a separate Git repository
- ✅ Shared across multiple projects
- ✅ Deployed to production without dependencies

**Verify independence:** Run `./verify_independence.sh`

No external dependencies • No parent imports • All paths relative • Docker isolated

## Overview

This project provides **10 MCP servers** that give AI agents access to various external systems and capabilities:

1. **PostgreSQL MCP** - Database operations (CRUD, queries)
2. **Filesystem MCP** - File system operations (read, write, list, delete)
3. **GitHub MCP** - GitHub API operations (repos, issues, PRs)
4. **Web Search MCP** - Web search and scraping (Brave API, HTML extraction)
5. **Redis MCP** - Cache and key-value operations (strings, hashes, lists)
6. **Slack MCP** - Slack integration (messages, channels, files)
7. **Python Exec MCP** - Safe Python code execution
8. **Vector DB MCP** - Semantic search with embeddings (Qdrant + OpenAI)
9. **HTTP API MCP** - Generic REST API calls (GET, POST, PUT, DELETE)
10. **Timeseries MCP** - Time series data operations (InfluxDB)

## Architecture

```
mcps/
├── compose.yaml              # Docker Compose configuration
├── .env.example             # Environment variable template
├── .env                     # Your environment variables (create this)
└── servers/                 # MCP server implementations
    ├── filesystem/
    │   ├── Dockerfile
    │   ├── requirements.txt
    │   └── server.py
    ├── github/
    ├── web-search/
    ├── redis/
    ├── slack/
    ├── python-exec/
    ├── vector-db/
    ├── http-api/
    └── timeseries/
```

## Quick Start

### 1. Setup Environment

```bash
# Copy environment template
cp .env.example .env

# Edit .env and add your API keys and tokens
nano .env
```

### 2. Start All Servers

```bash
# Start all MCP servers
docker compose up -d

# View logs
docker compose logs -f

# Check status
docker compose ps
```

### 3. Test Individual Servers

Each server exposes a health check endpoint:

```bash
# Test filesystem server
curl http://localhost:50002/health

# Test GitHub server
curl http://localhost:50003/health

# Test web search server
curl http://localhost:50004/health
```

### 4. Stop Servers

```bash
# Stop all servers
docker compose down

# Stop and remove volumes
docker compose down -v
```

## MCP Server Endpoints

| Server | Host Port | Container Port | Health Check |
|--------|-----------|----------------|--------------|
| PostgreSQL MCP | 50001 | 50000 | http://localhost:50001/health |
| Filesystem MCP | 50002 | 50000 | http://localhost:50002/health |
| GitHub MCP | 50003 | 50000 | http://localhost:50003/health |
| Web Search MCP | 50004 | 50000 | http://localhost:50004/health |
| Redis MCP | 50005 | 50000 | http://localhost:50005/health |
| Slack MCP | 50006 | 50000 | http://localhost:50006/health |
| Python Exec MCP | 50007 | 50000 | http://localhost:50007/health |
| Vector DB MCP | 50008 | 50000 | http://localhost:50008/health |
| HTTP API MCP | 50009 | 50000 | http://localhost:50009/health |
| Timeseries MCP | 50010 | 50000 | http://localhost:50010/health |

## Database Services

| Service | Host Port | Container Port | Type |
|---------|-----------|----------------|------|
| PostgreSQL | 54330 | 5432 | Relational DB |
| Redis | 63790 | 6379 | Key-Value Store |
| Qdrant | 63340 | 6333 | Vector DB |
| InfluxDB | 80870 | 8086 | Time Series DB |

## MCP Server Capabilities

### 1. Filesystem MCP (Port 50002)

**Tools:**
- `read_file` - Read file contents
- `write_file` - Write content to file
- `list_directory` - List directory contents
- `create_directory` - Create new directory
- `delete_file` - Delete file or directory
- `file_exists` - Check if file exists
- `get_file_info` - Get file metadata

**Example Request:**
```json
{
  "method": "tools/call",
  "params": {
    "name": "read_file",
    "arguments": {
      "path": "/workspace/example.txt"
    }
  }
}
```

### 2. GitHub MCP (Port 50003)

**Tools:**
- `get_repository` - Get repo information
- `list_issues` - List repository issues
- `create_issue` - Create new issue
- `get_pull_request` - Get PR details
- `list_pull_requests` - List PRs
- `create_pull_request` - Create new PR
- `get_file_contents` - Get file from repo
- `search_repositories` - Search GitHub repos
- `get_user` - Get user information

**Required:** `GITHUB_TOKEN` environment variable

### 3. Web Search MCP (Port 50004)

**Tools:**
- `brave_search` - Search web using Brave API (requires API key)
- `fetch_webpage` - Fetch HTML content
- `extract_text` - Extract clean text from webpage
- `extract_links` - Extract all links from webpage

**Optional:** `BRAVE_API_KEY` for search functionality

### 4. Redis MCP (Port 50005)

**Tools:**
- `redis_get` - Get value by key
- `redis_set` - Set key-value pair (with optional TTL)
- `redis_delete` - Delete key
- `redis_exists` - Check if key exists
- `redis_keys` - Get all keys matching pattern
- `redis_hset` - Set hash field
- `redis_hget` - Get hash field value
- `redis_hgetall` - Get all hash fields
- `redis_lpush` - Push value to list
- `redis_lrange` - Get range of list elements

### 5. Slack MCP (Port 50006)

**Tools:**
- `send_message` - Send message to channel
- `list_channels` - List all channels
- `get_channel_history` - Get recent messages
- `upload_file` - Upload file to channel

**Required:** `SLACK_BOT_TOKEN` environment variable

### 6. Python Exec MCP (Port 50007)

**Tools:**
- `execute_python` - Execute Python code and return output
- `evaluate_expression` - Evaluate Python expression

**Features:**
- Sandboxed execution
- Stdout/stderr capture
- Error handling with tracebacks
- Timeout protection (30s default)

**Security:** Limited capabilities, runs in isolated container

### 7. Vector DB MCP (Port 50008)

**Tools:**
- `create_collection` - Create new vector collection
- `upsert_vectors` - Add/update vectors (auto-embeds text)
- `search_similar` - Semantic similarity search
- `delete_collection` - Delete collection
- `list_collections` - List all collections

**Required:** `OPENAI_API_KEY` for embeddings

**Features:**
- Automatic text embedding using OpenAI
- Qdrant vector database backend
- Cosine similarity search

### 8. HTTP API MCP (Port 50009)

**Tools:**
- `http_get` - Make GET request
- `http_post` - Make POST request
- `http_put` - Make PUT request
- `http_delete` - Make DELETE request

**Features:**
- Custom headers support
- JSON request/response handling
- Configurable timeout (60s default)

### 9. Timeseries MCP (Port 50010)

**Tools:**
- `write_point` - Write data point to timeseries DB
- `query_range` - Query data within time range
- `query_last` - Query last N data points

**Backend:** InfluxDB 2.x

**Features:**
- Tags and fields support
- Flexible time range queries
- Measurement-based organization

### 10. PostgreSQL MCP (Port 50001)

**Status:** Uses pre-built image `mcp/postgres:latest`

**Note:** You may need to build or find this image separately, or replace with a custom implementation.

## Configuration

### Environment Variables

All configuration is done through environment variables in `.env` file:

```bash
# Required for specific servers
GITHUB_TOKEN=ghp_xxxxx
SLACK_BOT_TOKEN=xoxb-xxxxx
OPENAI_API_KEY=sk-xxxxx

# Optional for enhanced features
BRAVE_API_KEY=xxxxx
SERP_API_KEY=xxxxx

# Database connections (defaults work for Docker Compose)
POSTGRES_URL=postgresql://postgres:postgres@postgres-db:5432/mcpdb
REDIS_URL=redis://redis-db:6379
QDRANT_URL=http://qdrant:6333
INFLUXDB_URL=http://influxdb:8086
INFLUXDB_TOKEN=my-super-secret-token

# Filesystem paths
WORKSPACE_PATH=./workspace
DATA_PATH=./data
```

### Docker Compose Configuration

The `compose.yaml` file defines:
- **Project name:** `mcps`
- **Network:** `mcps-network` (bridge)
- **Volumes:** Persistent storage for databases
- **Port mappings:** 5xxxx range for MCP servers
- **Dependencies:** Automatic service ordering

### Persistent Data

Data is stored in named Docker volumes:
- `mcps-postgres-data` - PostgreSQL database
- `mcps-redis-data` - Redis persistence
- `mcps-qdrant-data` - Vector database
- `mcps-influxdb-data` - Time series data

## Using MCP Servers with AI Agents

### MCP Protocol Format

All servers follow the MCP (Model Context Protocol) specification:

**Request Format:**
```json
{
  "method": "tools/list",  // or "tools/call"
  "params": {
    "name": "tool_name",
    "arguments": {
      "arg1": "value1"
    }
  }
}
```

**Response Format:**
```json
{
  "result": { ... },  // Success result
  "error": {          // Or error
    "code": -32603,
    "message": "Error description"
  }
}
```

### Example: Using with LangChain

```python
import httpx
from langchain.tools import StructuredTool

async def call_mcp_tool(server_url: str, tool_name: str, **kwargs):
    """Call an MCP server tool."""
    async with httpx.AsyncClient() as client:
        response = await client.post(
            f"{server_url}/mcp",
            json={
                "method": "tools/call",
                "params": {
                    "name": tool_name,
                    "arguments": kwargs
                }
            }
        )
        data = response.json()
        if data.get("error"):
            raise Exception(data["error"]["message"])
        return data["result"]

# Create LangChain tools
github_search_tool = StructuredTool.from_function(
    func=lambda query: call_mcp_tool(
        "http://localhost:50003",
        "search_repositories",
        query=query
    ),
    name="search_github",
    description="Search GitHub repositories"
)

# Use with agent
from langchain.agents import AgentExecutor, create_openai_functions_agent
from langchain_openai import ChatOpenAI

llm = ChatOpenAI(model="gpt-4")
tools = [github_search_tool, ...]
agent = create_openai_functions_agent(llm, tools, prompt)
agent_executor = AgentExecutor(agent=agent, tools=tools)

result = agent_executor.invoke({"input": "Find popular Python repos"})
```

### Example: Direct HTTP Call

```bash
# List available tools
curl -X POST http://localhost:50002/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "method": "tools/list"
  }'

# Call a tool
curl -X POST http://localhost:50002/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "method": "tools/call",
    "params": {
      "name": "read_file",
      "arguments": {
        "path": "/workspace/test.txt"
      }
    }
  }'
```

## Troubleshooting

### Check Server Logs

```bash
# All servers
docker compose logs -f

# Specific server
docker compose logs -f filesystem-mcp

# Last 100 lines
docker compose logs --tail=100 github-mcp
```

### Common Issues

**Port already in use:**
```bash
# Change ports in compose.yaml
# Or stop conflicting service
lsof -ti:50002 | xargs kill
```

**Environment variables not loaded:**
```bash
# Verify .env file exists
ls -la .env

# Restart with fresh env
docker compose down
docker compose up -d
```

**Database connection issues:**
```bash
# Check if database is running
docker compose ps

# Restart database service
docker compose restart postgres-db
```

**Build failures:**
```bash
# Rebuild from scratch
docker compose build --no-cache

# Rebuild specific service
docker compose build --no-cache filesystem-mcp
```

## Development

### Adding a New MCP Server

1. Create server directory: `servers/my-server/`
2. Add Dockerfile, requirements.txt, server.py
3. Add service to `compose.yaml`
4. Update this README

### Testing Changes

```bash
# Rebuild and restart specific server
docker compose up -d --build filesystem-mcp

# View real-time logs
docker compose logs -f filesystem-mcp
```

### Server Template

```python
"""My MCP Server - Description."""
import os
from typing import Any, Dict, List, Optional
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
import uvicorn

app = FastAPI(title="My MCP Server")
SERVER_NAME = os.getenv("MCP_SERVER_NAME", "my-server")

class MCPRequest(BaseModel):
    method: str
    params: Optional[Dict[str, Any]] = None

class MCPResponse(BaseModel):
    result: Optional[Any] = None
    error: Optional[Dict[str, Any]] = None

@app.post("/mcp")
async def handle_mcp_request(request: MCPRequest) -> MCPResponse:
    try:
        if request.method == "tools/list":
            return MCPResponse(result=get_tool_definitions())
        elif request.method == "tools/call":
            # Handle tool calls
            pass
    except Exception as e:
        return MCPResponse(error={"code": -32603, "message": str(e)})

@app.get("/health")
async def health_check():
    return {"status": "healthy", "server": SERVER_NAME}

if __name__ == "__main__":
    uvicorn.run(app, host="0.0.0.0", port=50000)
```

## Security Considerations

1. **API Keys:** Store in `.env`, never commit to git
2. **Filesystem Access:** Limited to configured paths only
3. **Python Execution:** Sandboxed with resource limits
4. **Network Access:** Servers isolated in Docker network
5. **Database Access:** Authentication required for all DBs

## Performance

- All servers use **async/await** for non-blocking I/O
- FastAPI with Uvicorn for high performance
- Connection pooling for database services
- Configurable timeouts and limits

## 📚 Documentation

Complete documentation is available:

- **[USER_GUIDE.md](USER_GUIDE.md)** - Comprehensive usage guide with real-world examples
- **[TOOLS_REFERENCE.md](TOOLS_REFERENCE.md)** - Quick API reference for all tools
- **[DEPLOYMENT.md](DEPLOYMENT.md)** - Production deployment guide
- **Individual Server Docs** - Detailed guides in `servers/*/README.md`:
  - [PostgreSQL MCP](servers/postgres/README.md)
  - [Filesystem MCP](servers/filesystem/)
  - [GitHub MCP](servers/github/)
  - [Web Search MCP](servers/web-search/)
  - [Redis MCP](servers/redis/)
  - [Slack MCP](servers/slack/)
  - [Python Exec MCP](servers/python-exec/)
  - [Vector DB MCP](servers/vector-db/)
  - [HTTP API MCP](servers/http-api/)
  - [Timeseries MCP](servers/timeseries/)

## License

MIT License - See project root for details

## Contributing

1. Fork the repository
2. Create a feature branch
3. Add your MCP server
4. Test thoroughly
5. Submit a pull request

## Support

For issues and questions:
- Check the [Troubleshooting](#troubleshooting) section
- Review server logs
- Open an issue on GitHub

---

**Built with:** FastAPI, Docker, Model Context Protocol (MCP)

**For:** AI Agents with tool-calling capabilities
