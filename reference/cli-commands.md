# CLI Command Reference

Complete reference for the `libskills` CLI.

## Commands

### init — Scaffold a skill

```bash
libskills init [OPTIONS]
```

| Option | Description |
|--------|-------------|
| `-n, --name` | Library name |
| `-r, --repo` | GitHub repo (author/name) |
| `-l, --language` | Language: cpp, rust, python, go, js |
| `-t, --tags` | Comma-separated tags |
| `--version` | Library version (default: 0.1.0) |
| `--tier` | tier1 or tier2 (default: tier2) |
| `--group` | main or contrib (default: contrib) |
| `-o, --output` | Output directory (default: .libskills) |

Examples:
```bash
libskills init
libskills init -n spdlog -r gabime/spdlog -l cpp -t "logging,async,cpp"
libskills init -n mylib -r me/mylib -l python -t "web,api" -o ./skills/
```

### validate — Schema validation

```bash
libskills validate [PATH]
```

Validates `skill.json` against the LibSkills schema. Checks that all referenced files exist and P0 files are declared.

Examples:
```bash
libskills validate              # default: .libskills/
libskills validate .libskills/
libskills validate path/to/skill.json
```

### lint — Quality check

```bash
libskills lint [OPTIONS] [PATH]
```

| Option | Description |
|--------|-------------|
| `-f, --fix` | Auto-repair issues where possible |

Checks:
- Token counts (500–1500 per file)
- pitfalls.md ≥ 3 sections
- safety.md ≥ 2 sections
- overview.md exists
- examples/ ≥ 1 file
- tags ≥ 1 entry
- risk_level is valid

Examples:
```bash
libskills lint
libskills lint .libskills/
libskills lint --fix .libskills/
```

### update — Refresh registry index

```bash
libskills update [OPTIONS]
```

| Option | Description |
|--------|-------------|
| `-r, --registry` | Path to registry directory |

Downloads the registry index and builds the content search index.

Examples:
```bash
libskills update
libskills update -r /path/to/libskills-registry
```

### search — Keyword search

```bash
libskills search <KEYWORD>
```

Searches the local registry index by name, tags, and summary.

Examples:
```bash
libskills search logging
libskills search "async runtime"
```

### find — Semantic search

```bash
libskills find [OPTIONS] <QUERY...>
```

| Option | Description |
|--------|-------------|
| `-l, --limit` | Max results (default: 10) |
| `-t, --threshold` | Min score 0.0–1.0 |
| `-r, --registry` | Registry path |
| `--rebuild` | Force rebuild content index |

Searches skill file **content** using TF-IDF relevance scoring, not just metadata.

Examples:
```bash
libskills find async logger
libskills find "fast HTTP client" -l 5
libskills find web framework --rebuild
```

### get — Download a skill

```bash
libskills get [OPTIONS] <KEY>
```

| Option | Description |
|--------|-------------|
| `-r, --registry` | Registry path |

Downloads a skill to `~/.libskills/cache/{key}/`.

Examples:
```bash
libskills get cpp/gabime/spdlog
libskills get python/psf/requests -r ./registry/
```

### info — Show skill metadata

```bash
libskills info <KEY>
```

Displays full metadata for a cached skill, including trust score, tags, dependencies, and file listing.

Examples:
```bash
libskills info cpp/gabime/spdlog
```

### list — List cached skills

```bash
libskills list [OPTIONS]
```

| Option | Description |
|--------|-------------|
| `-v, --verbose` | Show additional detail |

Examples:
```bash
libskills list
libskills list -v
```

### cache — Manage cache

```bash
libskills cache <SUBCOMMAND>
```

| Subcommand | Description |
|------------|-------------|
| `clear` | Remove all cached skills |
| `prune` | Same as clear |
| `path` | Show cache directory paths |

Examples:
```bash
libskills cache path
libskills cache prune
```

### serve — HTTP API server

```bash
libskills serve [OPTIONS]
```

| Option | Description |
|--------|-------------|
| `-p, --port` | Port (default: 8701) |
| `-H, --host` | Host (default: 127.0.0.1) |
| `-r, --registry` | Registry path |

Starts an HTTP server exposing all skills via REST API.

Examples:
```bash
libskills serve
libskills serve -p 8080 -H 0.0.0.0
```

## Configuration

The CLI stores data in `~/.libskills/`:

```
~/.libskills/
├── index.json       # Local registry index
├── embedding.json   # Content search index
├── config.toml      # CLI configuration (future)
└── cache/           # Downloaded skills
    └── {lang}/{author}/{name}/
        ├── skill.json
        ├── overview.md
        └── ...
```
