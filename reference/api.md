# HTTP API Reference

The `libskills serve` command exposes a REST API for AI agents, IDEs, and CI systems.

## Starting the Server

```bash
libskills serve [--port 8701] [--host 127.0.0.1] [--registry /path/to/registry]
```

## Endpoints

### GET /health

Health check.

**Response:**
```json
{
  "status": "ok",
  "version": "0.1.0"
}
```

### GET /v1/skills

List all skills in the registry.

**Response:**
```json
{
  "schema": "libskills/v1",
  "version": 2,
  "updated_at": "2026-04-28T00:00:00Z",
  "skills": [
    {
      "key": "cpp/gabime/spdlog",
      "name": "spdlog",
      "language": "cpp",
      "tier": "tier1",
      "group": "main",
      "version": "1.14.2",
      "trust_score": 95,
      "tags": ["logging", "async"],
      "summary": "Fast C++ logging library"
    }
  ]
}
```

### GET /v1/skills/:lang/:author/:name

Get a complete skill with all file contents.

**Path parameters:**
- `lang` — Programming language (cpp, rust, python, go, js)
- `author` — GitHub username or organization
- `name` — Library name

**Response:**
```json
{
  "name": "spdlog",
  "repo": "gabime/spdlog",
  "language": "cpp",
  "tier": "tier1",
  "trust_score": 95,
  "risk_level": "medium",
  "tags": ["logging", "async"],
  "files": {
    "P0": ["overview.md", "pitfalls.md", "safety.md"],
    "P1": ["lifecycle.md", "threading.md", "best-practices.md"],
    "P2": ["performance.md"],
    "P3": ["examples/basic.cpp"]
  },
  "_contents": {
    "overview.md": "# spdlog — Overview\n\n...",
    "pitfalls.md": "# spdlog — Pitfalls\n\n...",
    "safety.md": "# spdlog — Safety\n\n..."
  }
}
```

The `_contents` field contains the full text of each knowledge file, keyed by filename.

### GET /v1/skills/:lang/:author/:name/:section

Get a single knowledge file as raw text. Add `.md` if not in the filename.

**Path parameters:**
- `section` — Filename, e.g., `pitfalls.md` or `overview.md`

**Response:** Raw markdown text (Content-Type: text/plain).

**Examples:**
```
GET /v1/skills/cpp/gabime/spdlog/pitfalls.md
GET /v1/skills/cpp/gabime/spdlog/pitfalls
GET /v1/skills/python/psf/requests/safety.md
```

### GET /v1/search

Keyword search against the registry index (name, tags, summary).

**Query parameters:**
- `q` — Search keyword (required)

**Response:**
```json
{
  "query": "http",
  "results": [
    {
      "key": "python/psf/requests",
      "name": "requests",
      "language": "python",
      "_score": 100
    }
  ]
}
```

Results are sorted by relevance score (descending).

### GET /v1/find

Semantic search against skill file **content** (TF-IDF).

**Query parameters:**
- `q` — Natural language query (required)
- `limit` — Max results (default: 10)

**Response:**
```json
{
  "query": "async HTTP client",
  "results": [
    {
      "key": "python/psf/requests",
      "score": 100.0,
      "raw_score": 0.042
    }
  ]
}
```

`score` is normalized to 0–100 relative to the top result. Results sorted by relevance.

## Error Responses

All endpoints return standard HTTP status codes:

| Code | Meaning |
|------|---------|
| 200 | Success |
| 404 | Skill or section not found |
| 500 | Internal server error |

Error body:
```json
{
  "error": "Skill 'cpp/invalid/name' not found"
}
```

## Usage Examples

### Python
```python
import requests

BASE = "http://localhost:8701"

# Find a skill
r = requests.get(f"{BASE}/v1/find", params={"q": "fast C++ logging"})
key = r.json()["results"][0]["key"]

# Get full skill
r = requests.get(f"{BASE}/v1/skills/{key}")
skill = r.json()

# Read pitfalls for AI context
pitfalls = skill["_contents"]["pitfalls.md"]
```

### curl
```bash
# Health
curl http://localhost:8701/health

# Search
curl "http://localhost:8701/v1/search?q=serialization"

# Semantic search
curl "http://localhost:8701/v1/find?q=web+API+framework&limit=3"

# Get section
curl "http://localhost:8701/v1/skills/cpp/gabime/spdlog/pitfalls.md"
```
