# MCP Servers User Guide

Complete guide to using the MCP (Model Context Protocol) servers for AI agents.

## Table of Contents

- [Overview](#overview)
- [Getting Started](#getting-started)
- [Server Guides](#server-guides)
- [Real-World Use Cases](#real-world-use-cases)
- [Integration Examples](#integration-examples)
- [Troubleshooting](#troubleshooting)
- [Best Practices](#best-practices)

---

## Overview

This collection provides 10 MCP servers that give AI agents powerful capabilities:

| Server | Use Case | Requires API Key |
|--------|----------|------------------|
| PostgreSQL | Database operations, data persistence | No |
| Filesystem | File management, data storage | No |
| GitHub | Code repository management | Yes (GitHub token) |
| Web Search | Internet research, web scraping | Optional (Brave API) |
| Redis | Caching, session management | No |
| Slack | Team communication, notifications | Yes (Slack bot token) |
| Python Exec | Code execution, calculations | No |
| Vector DB | Semantic search, embeddings | Yes (OpenAI API) |
| HTTP API | External API integration | No |
| Timeseries | Metrics, monitoring data | No |

---

## Getting Started

### 1. Start the Servers

```bash
./start.sh
```

This will:
- Create necessary directories
- Start all Docker containers
- Run health checks
- Display status

### 2. Verify Everything is Running

```bash
./test_servers.sh
```

### 3. Check Logs

```bash
# All servers
docker compose logs -f

# Specific server
docker compose logs -f postgres-mcp
```

---

## Server Guides

### 🗄️ PostgreSQL MCP (Port 50001)

**Purpose**: Structured data storage, complex queries, relational data

**When to Use**:
- Storing user data, application state
- Complex queries with JOIN operations
- Transactional data (orders, payments)
- Data that needs relationships and constraints

**Common Operations**:

```bash
# List all tables
curl -X POST http://localhost:50001/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "method": "tools/call",
    "params": {
      "name": "list_tables",
      "arguments": {}
    }
  }'

# Create a table
curl -X POST http://localhost:50001/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "method": "tools/call",
    "params": {
      "name": "execute_query",
      "arguments": {
        "query": "CREATE TABLE users (id SERIAL PRIMARY KEY, name TEXT, email TEXT, created_at TIMESTAMP DEFAULT NOW())"
      }
    }
  }'

# Insert data
curl -X POST http://localhost:50001/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "method": "tools/call",
    "params": {
      "name": "insert_row",
      "arguments": {
        "table_name": "users",
        "data": {
          "name": "Alice",
          "email": "alice@example.com"
        }
      }
    }
  }'

# Query with parameters
curl -X POST http://localhost:50001/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "method": "tools/call",
    "params": {
      "name": "execute_query",
      "arguments": {
        "query": "SELECT * FROM users WHERE email LIKE $1",
        "params": ["%@example.com"]
      }
    }
  }'
```

**Use Cases**:
- Customer relationship management
- E-commerce order tracking
- User authentication and profiles
- Content management systems

---

### 📁 Filesystem MCP (Port 50002)

**Purpose**: File operations, data persistence, configuration management

**When to Use**:
- Reading/writing configuration files
- Storing logs or reports
- Managing uploaded files
- Working with JSON/CSV/text data

**Common Operations**:

```bash
# Create a directory
curl -X POST http://localhost:50002/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "method": "tools/call",
    "params": {
      "name": "create_directory",
      "arguments": {
        "path": "/workspace/reports"
      }
    }
  }'

# Write a file
curl -X POST http://localhost:50002/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "method": "tools/call",
    "params": {
      "name": "write_file",
      "arguments": {
        "path": "/workspace/config.json",
        "content": "{\"app\": \"myapp\", \"version\": \"1.0\"}"
      }
    }
  }'

# Read a file
curl -X POST http://localhost:50002/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "method": "tools/call",
    "params": {
      "name": "read_file",
      "arguments": {
        "path": "/workspace/config.json"
      }
    }
  }'

# List directory contents
curl -X POST http://localhost:50002/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "method": "tools/call",
    "params": {
      "name": "list_directory",
      "arguments": {
        "path": "/workspace"
      }
    }
  }'
```

**Use Cases**:
- Configuration management
- Log file analysis
- Data export/import
- Document generation

---

### 🐙 GitHub MCP (Port 50003)

**Purpose**: Repository management, code collaboration

**When to Use**:
- Searching for code examples
- Creating issues and PRs
- Reading repository content
- Automating code reviews

**Common Operations**:

```bash
# Search repositories
curl -X POST http://localhost:50003/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "method": "tools/call",
    "params": {
      "name": "search_repositories",
      "arguments": {
        "query": "machine learning python stars:>1000"
      }
    }
  }'

# Get repository info
curl -X POST http://localhost:50003/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "method": "tools/call",
    "params": {
      "name": "get_repository",
      "arguments": {
        "repo": "owner/repo-name"
      }
    }
  }'

# List issues
curl -X POST http://localhost:50003/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "method": "tools/call",
    "params": {
      "name": "list_issues",
      "arguments": {
        "repo": "owner/repo-name",
        "state": "open"
      }
    }
  }'

# Create an issue
curl -X POST http://localhost:50003/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "method": "tools/call",
    "params": {
      "name": "create_issue",
      "arguments": {
        "repo": "owner/repo-name",
        "title": "Bug: Login not working",
        "body": "Users are unable to login with valid credentials"
      }
    }
  }'
```

**Use Cases**:
- Code discovery and research
- Automated issue triaging
- PR creation and reviews
- Documentation updates

---

### 🔍 Web Search MCP (Port 50004)

**Purpose**: Internet research, content scraping, data gathering

**When to Use**:
- Searching for information online
- Extracting data from web pages
- Monitoring websites
- Content aggregation

**Common Operations**:

```bash
# Search the web (requires Brave API key)
curl -X POST http://localhost:50004/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "method": "tools/call",
    "params": {
      "name": "brave_search",
      "arguments": {
        "query": "best practices for REST API design",
        "count": 10
      }
    }
  }'

# Fetch webpage content
curl -X POST http://localhost:50004/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "method": "tools/call",
    "params": {
      "name": "fetch_webpage",
      "arguments": {
        "url": "https://example.com"
      }
    }
  }'

# Extract clean text
curl -X POST http://localhost:50004/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "method": "tools/call",
    "params": {
      "name": "extract_text",
      "arguments": {
        "url": "https://example.com/article"
      }
    }
  }'

# Extract all links
curl -X POST http://localhost:50004/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "method": "tools/call",
    "params": {
      "name": "extract_links",
      "arguments": {
        "url": "https://news.ycombinator.com"
      }
    }
  }'
```

**Use Cases**:
- Research and fact-checking
- Competitive analysis
- News monitoring
- Web scraping projects

---

### 💾 Redis MCP (Port 50005)

**Purpose**: Caching, session storage, real-time data

**When to Use**:
- Caching API responses
- Session management
- Rate limiting
- Real-time counters and leaderboards

**Common Operations**:

```bash
# Set a value with TTL
curl -X POST http://localhost:50005/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "method": "tools/call",
    "params": {
      "name": "redis_set",
      "arguments": {
        "key": "session:user123",
        "value": "{\"username\": \"alice\", \"role\": \"admin\"}",
        "ttl": 3600
      }
    }
  }'

# Get a value
curl -X POST http://localhost:50005/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "method": "tools/call",
    "params": {
      "name": "redis_get",
      "arguments": {
        "key": "session:user123"
      }
    }
  }'

# Use hash for structured data
curl -X POST http://localhost:50005/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "method": "tools/call",
    "params": {
      "name": "redis_hset",
      "arguments": {
        "key": "user:123",
        "field": "email",
        "value": "alice@example.com"
      }
    }
  }'

# Get all hash fields
curl -X POST http://localhost:50005/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "method": "tools/call",
    "params": {
      "name": "redis_hgetall",
      "arguments": {
        "key": "user:123"
      }
    }
  }'
```

**Use Cases**:
- Session storage
- API response caching
- Rate limiting
- Real-time analytics

---

### 💬 Slack MCP (Port 50006)

**Purpose**: Team communication, notifications, alerts

**When to Use**:
- Sending notifications
- Automated alerts
- Bot interactions
- Team updates

**Common Operations**:

```bash
# Send a message
curl -X POST http://localhost:50006/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "method": "tools/call",
    "params": {
      "name": "send_message",
      "arguments": {
        "channel": "#general",
        "text": "Deployment completed successfully! 🚀"
      }
    }
  }'

# List channels
curl -X POST http://localhost:50006/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "method": "tools/call",
    "params": {
      "name": "list_channels",
      "arguments": {}
    }
  }'

# Upload a file
curl -X POST http://localhost:50006/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "method": "tools/call",
    "params": {
      "name": "upload_file",
      "arguments": {
        "channel": "#reports",
        "content": "Q4 Sales Report: Revenue increased by 25%",
        "filename": "q4_report.txt"
      }
    }
  }'
```

**Use Cases**:
- Deployment notifications
- Error alerts
- Daily reports
- Team coordination

---

### 🐍 Python Exec MCP (Port 50007)

**Purpose**: Code execution, calculations, data processing

**When to Use**:
- Complex calculations
- Data transformation
- Algorithm testing
- Quick prototyping

**Common Operations**:

```bash
# Execute Python code
curl -X POST http://localhost:50007/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "method": "tools/call",
    "params": {
      "name": "execute_python",
      "arguments": {
        "code": "import json\ndata = {\"users\": 150, \"revenue\": 45000}\nprint(json.dumps(data, indent=2))"
      }
    }
  }'

# Evaluate expression
curl -X POST http://localhost:50007/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "method": "tools/call",
    "params": {
      "name": "evaluate_expression",
      "arguments": {
        "expression": "sum([1, 2, 3, 4, 5]) / 5"
      }
    }
  }'

# Data processing
curl -X POST http://localhost:50007/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "method": "tools/call",
    "params": {
      "name": "execute_python",
      "arguments": {
        "code": "import statistics\ndata = [10, 20, 30, 40, 50]\nprint(f\"Mean: {statistics.mean(data)}\")\nprint(f\"Median: {statistics.median(data)}\")\nprint(f\"Stdev: {statistics.stdev(data)}\")"
      }
    }
  }'
```

**Use Cases**:
- Mathematical calculations
- Data analysis
- Text processing
- Quick scripts

---

### 🔎 Vector DB MCP (Port 50008)

**Purpose**: Semantic search, similarity matching, embeddings

**When to Use**:
- Document search
- Recommendation systems
- Duplicate detection
- Content similarity

**Common Operations**:

```bash
# Create a collection
curl -X POST http://localhost:50008/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "method": "tools/call",
    "params": {
      "name": "create_collection",
      "arguments": {
        "collection_name": "documentation",
        "vector_size": 1536
      }
    }
  }'

# Add documents
curl -X POST http://localhost:50008/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "method": "tools/call",
    "params": {
      "name": "upsert_vectors",
      "arguments": {
        "collection_name": "documentation",
        "texts": [
          "How to deploy your application to production",
          "Setting up database connections",
          "Configuring environment variables"
        ],
        "metadatas": [
          {"category": "deployment", "page": 1},
          {"category": "database", "page": 2},
          {"category": "config", "page": 3}
        ]
      }
    }
  }'

# Search for similar content
curl -X POST http://localhost:50008/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "method": "tools/call",
    "params": {
      "name": "search_similar",
      "arguments": {
        "collection_name": "documentation",
        "query": "How do I connect to the database?",
        "limit": 3
      }
    }
  }'
```

**Use Cases**:
- Documentation search
- FAQ systems
- Content recommendations
- Duplicate detection

---

### 🌐 HTTP API MCP (Port 50009)

**Purpose**: External API integration, webhooks, REST calls

**When to Use**:
- Calling external APIs
- Webhook triggers
- Third-party integrations
- API testing

**Common Operations**:

```bash
# GET request
curl -X POST http://localhost:50009/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "method": "tools/call",
    "params": {
      "name": "http_get",
      "arguments": {
        "url": "https://api.github.com/repos/microsoft/vscode",
        "headers": {
          "Accept": "application/json"
        }
      }
    }
  }'

# POST request
curl -X POST http://localhost:50009/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "method": "tools/call",
    "params": {
      "name": "http_post",
      "arguments": {
        "url": "https://api.example.com/webhook",
        "data": {
          "event": "user_signup",
          "user_id": "12345"
        },
        "headers": {
          "Authorization": "Bearer YOUR_TOKEN"
        }
      }
    }
  }'
```

**Use Cases**:
- Payment processing (Stripe, PayPal)
- SMS/Email services (Twilio, SendGrid)
- Cloud services (AWS, GCP)
- Analytics tracking

---

### 📊 Timeseries MCP (Port 50010)

**Purpose**: Metrics, monitoring, time-based data

**When to Use**:
- Application metrics
- System monitoring
- Business analytics
- IoT sensor data

**Common Operations**:

```bash
# Write a data point
curl -X POST http://localhost:50010/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "method": "tools/call",
    "params": {
      "name": "write_point",
      "arguments": {
        "measurement": "cpu_usage",
        "fields": {
          "value": 75.5,
          "load_avg": 2.3
        },
        "tags": {
          "host": "server-01",
          "region": "us-east"
        }
      }
    }
  }'

# Query time range
curl -X POST http://localhost:50010/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "method": "tools/call",
    "params": {
      "name": "query_range",
      "arguments": {
        "measurement": "cpu_usage",
        "start": "-1h"
      }
    }
  }'

# Query last N points
curl -X POST http://localhost:50010/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "method": "tools/call",
    "params": {
      "name": "query_last",
      "arguments": {
        "measurement": "cpu_usage",
        "limit": 10
      }
    }
  }'
```

**Use Cases**:
- Server monitoring
- Application performance
- Business KPIs
- Sensor data logging

---

## Real-World Use Cases

### Use Case 1: Automated Customer Support Bot

**Scenario**: Build a bot that answers customer questions using documentation

**Servers Used**:
- Vector DB: Store and search documentation
- Slack: Receive questions and send answers
- PostgreSQL: Log all interactions

**Workflow**:
1. User asks question in Slack
2. Vector DB searches for relevant documentation
3. Python Exec processes and formats answer
4. Slack sends response to user
5. PostgreSQL logs the interaction

---

### Use Case 2: CI/CD Automation

**Scenario**: Automated deployment with notifications

**Servers Used**:
- GitHub: Monitor PRs and commits
- Python Exec: Run tests and builds
- Slack: Send deployment notifications
- Timeseries: Log deployment metrics

**Workflow**:
1. GitHub detects new PR
2. Python Exec runs tests
3. If tests pass, deploy
4. Slack notifies team
5. Timeseries records deployment time

---

### Use Case 3: Data Analysis Pipeline

**Scenario**: Process and analyze business data

**Servers Used**:
- PostgreSQL: Source database
- Redis: Cache results
- Python Exec: Data processing
- Filesystem: Save reports

**Workflow**:
1. PostgreSQL fetches sales data
2. Python Exec calculates metrics
3. Redis caches frequently accessed data
4. Filesystem saves CSV reports

---

## Integration Examples

### Using with Python

```python
import httpx
import asyncio

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

# Example usage
async def main():
    # Store data in Redis
    await call_mcp_tool(
        "http://localhost:50005",
        "redis_set",
        key="user:123",
        value="Alice"
    )

    # Execute Python code
    result = await call_mcp_tool(
        "http://localhost:50007",
        "evaluate_expression",
        expression="2 ** 10"
    )
    print(f"Result: {result}")

    # Search GitHub
    repos = await call_mcp_tool(
        "http://localhost:50003",
        "search_repositories",
        query="fastapi python"
    )
    print(f"Found {len(repos)} repositories")

asyncio.run(main())
```

### Using with Node.js

```javascript
const axios = require('axios');

async function callMCPTool(serverUrl, toolName, args) {
  const response = await axios.post(`${serverUrl}/mcp`, {
    method: 'tools/call',
    params: {
      name: toolName,
      arguments: args
    }
  });

  if (response.data.error) {
    throw new Error(response.data.error.message);
  }

  return response.data.result;
}

// Example usage
async function main() {
  // Write a file
  await callMCPTool('http://localhost:50002', 'write_file', {
    path: '/workspace/data.json',
    content: JSON.stringify({ message: 'Hello from Node.js!' })
  });

  // Send Slack message
  await callMCPTool('http://localhost:50006', 'send_message', {
    channel: '#general',
    text: 'File saved successfully!'
  });
}

main();
```

---

## Troubleshooting

### Server Won't Start

**Problem**: Container fails to start

**Solutions**:
```bash
# Check logs
docker compose logs <service-name>

# Rebuild from scratch
docker compose down
docker compose build --no-cache
docker compose up -d

# Check port conflicts
lsof -i :50001
```

### API Key Errors

**Problem**: "API key not configured"

**Solutions**:
1. Check `.env` file exists
2. Verify API key is correct
3. Restart containers after updating `.env`:
   ```bash
   docker compose down
   docker compose up -d
   ```

### Connection Refused

**Problem**: Can't connect to server

**Solutions**:
```bash
# Check if containers are running
docker compose ps

# Verify port is exposed
docker compose port <service> 50000

# Test health endpoint
curl http://localhost:50001/health
```

### Database Connection Issues

**Problem**: PostgreSQL/Redis won't connect

**Solutions**:
```bash
# Check database is running
docker compose ps postgres-db

# Verify connection string
echo $POSTGRES_URL

# Restart database
docker compose restart postgres-db
docker compose restart postgres-mcp
```

---

## Best Practices

### 1. Error Handling

Always check for errors in responses:

```python
result = await call_mcp_tool(...)
if result.get("error"):
    print(f"Error: {result['error']['message']}")
    # Handle error appropriately
```

### 2. Rate Limiting

Respect API rate limits:
- Use Redis to cache frequently accessed data
- Implement exponential backoff for retries
- Monitor rate limit headers

### 3. Security

- Never commit `.env` with real API keys
- Use environment-specific `.env` files
- Rotate API keys regularly
- Use read-only tokens when possible

### 4. Performance

- Cache results in Redis when appropriate
- Use connection pooling
- Batch operations when possible
- Monitor response times with Timeseries MCP

### 5. Monitoring

```bash
# Monitor all logs
docker compose logs -f

# Monitor specific service
docker compose logs -f postgres-mcp

# Check resource usage
docker stats
```

---

## Next Steps

1. Read [TOOLS_REFERENCE.md](TOOLS_REFERENCE.md) for complete API reference
2. Explore [README.md](README.md) for setup details
3. Check individual server READMEs in `servers/*/README.md`
4. Join our community for support

---

**Need Help?**
- Check logs: `docker compose logs -f`
- Run tests: `./test_servers.sh`
- Verify health: `curl http://localhost:5000X/health`
