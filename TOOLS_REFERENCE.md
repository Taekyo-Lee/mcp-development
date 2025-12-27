# MCP Servers - Tools Quick Reference

## PostgreSQL MCP (Port 50001)

| Tool | Description | Required Args | Optional Args |
|------|-------------|---------------|---------------|
| `execute_query` | Execute SQL query | `query` | `params` |
| `list_tables` | List all tables in database | - | - |
| `describe_table` | Get table schema and columns | `table_name` | - |
| `insert_row` | Insert a row into table | `table_name`, `data` | - |
| `update_row` | Update rows in table | `table_name`, `data`, `where_clause` | - |
| `delete_row` | Delete rows from table | `table_name`, `where_clause` | - |

## Filesystem MCP (Port 50002)

| Tool | Description | Required Args | Optional Args |
|------|-------------|---------------|---------------|
| `read_file` | Read file contents | `path` | - |
| `write_file` | Write content to file | `path`, `content` | - |
| `list_directory` | List directory contents | `path` | - |
| `create_directory` | Create new directory | `path` | - |
| `delete_file` | Delete file or directory | `path` | - |
| `file_exists` | Check if file exists | `path` | - |
| `get_file_info` | Get file metadata | `path` | - |

## GitHub MCP (Port 50003)

| Tool | Description | Required Args | Optional Args |
|------|-------------|---------------|---------------|
| `get_repository` | Get repo information | `repo` | - |
| `list_issues` | List repository issues | `repo` | `state` |
| `create_issue` | Create new issue | `repo`, `title` | `body` |
| `get_pull_request` | Get PR details | `repo`, `number` | - |
| `list_pull_requests` | List PRs | `repo` | `state` |
| `create_pull_request` | Create new PR | `repo`, `title`, `head`, `base` | `body` |
| `get_file_contents` | Get file from repo | `repo`, `path` | `ref` |
| `search_repositories` | Search GitHub repos | `query` | - |
| `get_user` | Get user information | `username` | - |

## Web Search MCP (Port 50004)

| Tool | Description | Required Args | Optional Args |
|------|-------------|---------------|---------------|
| `brave_search` | Search web (requires API key) | `query` | `count` |
| `fetch_webpage` | Fetch HTML content | `url` | - |
| `extract_text` | Extract clean text | `url` | - |
| `extract_links` | Extract all links | `url` | - |

## Redis MCP (Port 50005)

| Tool | Description | Required Args | Optional Args |
|------|-------------|---------------|---------------|
| `redis_get` | Get value by key | `key` | - |
| `redis_set` | Set key-value pair | `key`, `value` | `ttl` |
| `redis_delete` | Delete key | `key` | - |
| `redis_exists` | Check if key exists | `key` | - |
| `redis_keys` | Get keys matching pattern | - | `pattern` |
| `redis_hset` | Set hash field | `key`, `field`, `value` | - |
| `redis_hget` | Get hash field value | `key`, `field` | - |
| `redis_hgetall` | Get all hash fields | `key` | - |
| `redis_lpush` | Push value to list | `key`, `value` | - |
| `redis_lrange` | Get range of list elements | `key` | `start`, `stop` |

## Slack MCP (Port 50006)

| Tool | Description | Required Args | Optional Args |
|------|-------------|---------------|---------------|
| `send_message` | Send message to channel | `channel`, `text` | - |
| `list_channels` | List all channels | - | - |
| `get_channel_history` | Get recent messages | `channel` | `limit` |
| `upload_file` | Upload file to channel | `channel`, `content`, `filename` | - |

## Python Exec MCP (Port 50007)

| Tool | Description | Required Args | Optional Args |
|------|-------------|---------------|---------------|
| `execute_python` | Execute Python code | `code` | - |
| `evaluate_expression` | Evaluate Python expression | `expression` | - |

## Vector DB MCP (Port 50008)

| Tool | Description | Required Args | Optional Args |
|------|-------------|---------------|---------------|
| `create_collection` | Create vector collection | `collection_name` | `vector_size` |
| `upsert_vectors` | Add/update vectors | `collection_name`, `texts` | `metadatas` |
| `search_similar` | Semantic similarity search | `collection_name`, `query` | `limit` |
| `delete_collection` | Delete collection | `collection_name` | - |
| `list_collections` | List all collections | - | - |

## HTTP API MCP (Port 50009)

| Tool | Description | Required Args | Optional Args |
|------|-------------|---------------|---------------|
| `http_get` | Make GET request | `url` | `headers` |
| `http_post` | Make POST request | `url` | `data`, `headers` |
| `http_put` | Make PUT request | `url` | `data`, `headers` |
| `http_delete` | Make DELETE request | `url` | `headers` |

## Timeseries MCP (Port 50010)

| Tool | Description | Required Args | Optional Args |
|------|-------------|---------------|---------------|
| `write_point` | Write data point | `measurement`, `fields` | `tags` |
| `query_range` | Query data in time range | `measurement`, `start` | `stop` |
| `query_last` | Query last N points | `measurement` | `limit` |

---

## Example Requests

### PostgreSQL - List Tables
```json
POST http://localhost:50001/mcp
{
  "method": "tools/call",
  "params": {
    "name": "list_tables",
    "arguments": {}
  }
}
```

### PostgreSQL - Execute Query
```json
POST http://localhost:50001/mcp
{
  "method": "tools/call",
  "params": {
    "name": "execute_query",
    "arguments": {
      "query": "SELECT * FROM users WHERE age > $1",
      "params": [18]
    }
  }
}
```

### Read a file
```json
POST http://localhost:50002/mcp
{
  "method": "tools/call",
  "params": {
    "name": "read_file",
    "arguments": {
      "path": "/workspace/data.txt"
    }
  }
}
```

### Search GitHub
```json
POST http://localhost:50003/mcp
{
  "method": "tools/call",
  "params": {
    "name": "search_repositories",
    "arguments": {
      "query": "machine learning python"
    }
  }
}
```

### Redis Set/Get
```json
POST http://localhost:50005/mcp
{
  "method": "tools/call",
  "params": {
    "name": "redis_set",
    "arguments": {
      "key": "user:1:name",
      "value": "Alice",
      "ttl": 3600
    }
  }
}
```

### Execute Python
```json
POST http://localhost:50007/mcp
{
  "method": "tools/call",
  "params": {
    "name": "execute_python",
    "arguments": {
      "code": "import math\nprint(math.pi)"
    }
  }
}
```

### Vector Search
```json
POST http://localhost:50008/mcp
{
  "method": "tools/call",
  "params": {
    "name": "search_similar",
    "arguments": {
      "collection_name": "documents",
      "query": "How do I deploy to production?",
      "limit": 5
    }
  }
}
```

### HTTP API Call
```json
POST http://localhost:50009/mcp
{
  "method": "tools/call",
  "params": {
    "name": "http_post",
    "arguments": {
      "url": "https://api.example.com/data",
      "data": {
        "key": "value"
      },
      "headers": {
        "Authorization": "Bearer token"
      }
    }
  }
}
```

### Write Timeseries Data
```json
POST http://localhost:50010/mcp
{
  "method": "tools/call",
  "params": {
    "name": "write_point",
    "arguments": {
      "measurement": "temperature",
      "fields": {
        "value": 23.5,
        "unit": "celsius"
      },
      "tags": {
        "sensor": "sensor-01",
        "location": "office"
      }
    }
  }
}
```

## Response Format

All tools return responses in this format:

**Success:**
```json
{
  "result": {
    // Tool-specific result data
  },
  "error": null
}
```

**Error:**
```json
{
  "result": null,
  "error": {
    "code": -32603,
    "message": "Error description"
  }
}
```

## Common Response Codes

| Code | Description |
|------|-------------|
| 200 | Success |
| 400 | Bad Request (invalid tool or arguments) |
| 403 | Forbidden (unauthorized access) |
| 500 | Internal Server Error |

MCP error codes:
- `-32700` - Parse error
- `-32600` - Invalid request
- `-32601` - Method not found
- `-32602` - Invalid params
- `-32603` - Internal error

