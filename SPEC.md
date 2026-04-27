# LibSkills Specification v1.0

This document defines the **LibSkills Standard** — the format, conventions, and protocol for packaging operational knowledge about open-source libraries so that AI agents can use them safely. Schema version: `libskills/v1`.

## Version History

---

## 1. Core Concept

LibSkills is a **standard**, not a platform.

Every library repository can ship a `.libskills/` directory containing structured knowledge files. AI agents, IDEs, and CI systems discover and consume these files to avoid hallucinations, incorrect API usage, and life-cycle bugs.

The core insight: **the hard part is no longer writing code — it's judging, integrating, and knowing the constraints.** LibSkills is a *risk-perception layer*: it tells AI agents where the library will break, not what it can do.

---

## 2. The `.libskills/` Directory Convention

### 2.1 Location

A skill lives in a `.libskills/` directory at the root of the library's repository:

```
your-library/
├── .libskills/
│   ├── skill.json
│   ├── overview.md
│   ├── pitfalls.md
│   ├── safety.md
│   ├── lifecycle.md
│   ├── threading.md
│   ├── best-practices.md
│   ├── performance.md
│   └── examples/
│       └── basic.cpp
├── src/
├── README.md
└── ...
```

### 2.2 Why `.libskills/` (repository-level)

- **Decentralized**: Any repo can self-host its skill. No registration required.
- **Discoverable**: AI agents and tools can check for `.libskills/skill.json` at standard locations.
- **Versioned with the library**: The skill lives alongside the code it describes, making version alignment natural.
- **Standard over platform**: Like `.editorconfig`, `package.json`, or `Dockerfile`, the convention is the product.

### 2.3 Version Alignment

A skill's `version` field in `skill.json` declares which library version it targets. When a library releases a new version, the `.libskills/` files should be updated accordingly.

The `skill_version` field tracks the version of the skill content itself (semantic versioning), independent of the library version. A skill can be improved (new pitfalls, better examples) without the library changing.

### 2.4 `repo_skill` Flag

When a skill lives in the library's own repository, `skill.json` MUST set `repo_skill: true`. This distinguishes self-hosted skills from registry-only skills and signals to AI agents that the skill is maintained alongside the code.

---

## 3. Skill Metadata (`skill.json`)

### 3.1 Schema Example

```jsonc
{
  "name": "spdlog",
  "repo": "gabime/spdlog",
  "language": "cpp",
  "tier": "tier1",
  "group": "main",
  "version": "1.14.2",
  "skill_version": "0.1.0",
  "schema": "libskills/v1",
  "skill_type": "library",
  "repo_skill": true,

  "trust_score": 95,
  "verified": true,
  "official": true,
  "updated_at": "2026-04-25T00:00:00Z",

  "trust_score_sources": {
    "official_review": 40,
    "stars": 20,
    "community_votes": 20,
    "update_freshness": 15,
    "issue_health": 5
  },

  "completeness": 85,
  "risk_level": "medium",

  "tags": [
    "logging",
    "async",
    "thread-safe",
    "header-only"
  ],

  "compatibility": {
    "c++": ["17", "20", "23"],
    "compilers": ["clang>=16", "gcc>=11", "msvc>=2022"],
    "platforms": ["linux-x64", "macos-arm64", "windows-x64"]
  },

  "dependencies": {
    "required": ["fmt"],
    "optional": [],
    "skills": ["cpp/fmtlib/fmt"]
  },

  "read_order": [
    "overview.md",
    "pitfalls.md",
    "safety.md"
  ],

  "files": {
    "P0": [
      "overview.md",
      "pitfalls.md",
      "safety.md"
    ],
    "P1": [
      "lifecycle.md",
      "threading.md",
      "best-practices.md"
    ],
    "P2": [
      "performance.md"
    ],
    "P3": [
      "examples/basic.cpp"
    ]
  },

  "inherits": null
}
```

### 3.2 Required Fields

| Field | Type | Description |
|-------|------|-------------|
| `name` | string | Library name |
| `repo` | string | GitHub repository (`author/name`) |
| `language` | string | Primary language: `cpp`, `rust`, `python`, `go`, `js` |
| `tier` | string | `tier1` (curated) or `tier2` (community) |
| `group` | string | `main` (de-facto standard) or `contrib` (niche/smaller) |
| `version` | string | Library version this skill targets |
| `skill_version` | string | Semantic version of the skill content (`0.1.0`) |
| `schema` | string | `libskills/v0` |
| `skill_type` | string | One of 10 types (see §11) |
| `repo_skill` | boolean | `true` if skill lives in the library's own repo |
| `trust_score` | integer | 0–100 |
| `updated_at` | string | ISO 8601 timestamp |
| `tags` | string[] | At least 1 tag for search/discovery |
| `read_order` | string[] | File paths in recommended reading order (P0 only) |
| `files` | object | Files grouped by priority: `P0`, `P1`, `P2`, `P3` |
| `risk_level` | string | `high`, `medium`, or `low` — guides AI consumption priority |

### 3.3 Optional Fields

| Field | Type | Description |
|-------|------|-------------|
| `verified` | boolean | Whether the skill has passed a review |
| `official` | boolean | Whether this is an official (maintainer-authored) skill |
| `completeness` | integer | 0–100, auto-calculated from file presence |
| `compatibility` | object | Language versions, compilers, platforms |
| `dependencies` | object | Required/optional dependencies + their skills |
| `trust_score_sources` | object | Breakdown of trust score components |
| `inherits` | string | Parent skill key if this skill inherits (future) |
| `community_rating` | object | Community scores (future) |

### 3.4 Trust Score Calculation

| Component | Max Score | Source |
|-----------|-----------|--------|
| Official Review | 40 | Maintainer review |
| Stars | 20 | GitHub stars tier |
| Community Votes | 20 | User ratings and usage |
| Update Freshness | 15 | Skill updated within 60 days of library release |
| Issue Health | 5 | Low open issue count relative to stars |

---

## 4. Knowledge Files

### 4.1 Priority System (Reading Protocol)

Every file in a skill has a priority level that determines when an AI agent should read it.

| Priority | Reading Strategy | Files |
|----------|-----------------|-------|
| **P0** | Read before generating any code | `overview.md`, `pitfalls.md`, `safety.md` |
| **P1** | Read when using the relevant feature | `lifecycle.md`, `threading.md`, `best-practices.md` |
| **P2** | Read on demand | `performance.md` |
| **P3** | Reference only | `examples/*` |

This protocol exists because:
- LLMs are sequential reasoners — reading order directly affects output quality
- Context windows are finite — not every file needs to be loaded at once
- The highest-cost knowledge (where the library breaks) must be read first

### 4.2 Priority Rules

- **P0 test**: *"If skipped, the AI might produce code that crashes, leaks, or silently corrupts data."*
- **P1 test**: *"If skipped, the AI might produce correct but suboptimal code."*
- P2 and P3 are entirely on-demand. The AI decides when to read them.

### 4.3 Token Limits

Each file MUST be **500–1500 tokens** (not characters). This keeps each chunk small enough for an AI agent to consume efficiently and ensures every file is independently useful.

### 4.4 `overview.md` [P0] — REQUIRED

Brief description of the library, its purpose, primary use cases, and when NOT to use it.

### 4.5 `pitfalls.md` [P0] — REQUIRED

**The most important file.** Common mistakes, anti-patterns, and hidden constraints. What NOT to do. Minimum 3 entries.

```markdown
## Anti-Patterns

### Do NOT use std::endl
`spdlog` is binary-safe. Using `std::endl` flushes the buffer on every write.
Always use `\n` or let the logger handle flushing.

### Do NOT pass temporary strings for format args
// BAD
spdlog::info("Value: {}", std::to_string(x));  // Heap allocation
// GOOD
spdlog::info("Value: {}", x);  // spdlog handles formatting

### Do NOT use default logger in static destructors
The default logger may already be destroyed. See `lifecycle.md`.
```

### 4.6 `safety.md` [P0] — REQUIRED

Red lines — conditions that must NEVER occur. Minimum 2 entries. If an AI agent generates code that violates a safety rule, it should stop and warn.

```markdown
## Red Lines

- NEVER use logger after fork() without recreating it
- NEVER destroy logger before flush
- NEVER share `basic_file_sink` across threads without synchronization
- NEVER use `%s` format strings — always use {} formatting
```

### 4.7 `lifecycle.md` [P1]

Initialization, shutdown, and ordering constraints.

### 4.8 `threading.md` [P1]

Thread safety guarantees, async behavior, and concurrency constraints.

### 4.9 `best-practices.md` [P1]

Recommended usage patterns, proven combinations, and architecture decisions that make the library work better in real projects.

**Distinction from pitfalls.md**: If following a pattern wrongly causes a crash, it goes in `pitfalls.md`. If it just makes code less elegant or slightly slower, it goes in `best-practices.md`.

| Criterion | pitfalls.md | best-practices.md |
|-----------|-------------|-------------------|
| Missing it → | crash / bug / leak | code works but could be better |
| Priority | P0 (always read) | P1 (read on use) |
| Content | mistakes, anti-patterns, red lines | recommended patterns, combinations |

### 4.10 `performance.md` [P2]

Throughput, latency, blocking behavior, allocation patterns.

### 4.11 `examples/` [P3]

Minimal working examples. One file per example. Self-contained and compilable/runnable. At least 1 example required.

---

## 5. Registry & Skill Discovery

### 5.1 Two-Tier Architecture

The LibSkills ecosystem uses a **hybrid discovery model**:

1. **Repository-level (`.libskills/`)** — the primary source. Any library can self-host its skill. AI agents check the repo directly.
2. **Aggregation registry** — a centralized index that crawls GitHub for `.libskills/` directories, providing search and caching.

A skill is valid whether it exists only in the library's repo, only in the registry, or both.

### 5.2 Aggregation Index (`index.json`)

The registry aggregates skills discovered from GitHub repositories into a searchable index.

```jsonc
{
  "schema": "libskills/v1",
  "version": 1,
  "updated_at": "2026-04-25T00:00:00Z",
  "skills": [
    {
      "key": "cpp/gabime/spdlog",
      "name": "spdlog",
      "language": "cpp",
      "tier": "tier1",
      "group": "main",
      "version": "1.14.2",
      "trust_score": 95,
      "tags": ["logging", "async", "thread-safe"],
      "summary": "Fast C++ logging library with async support",
      "repo_source_url": "https://github.com/gabime/spdlog",
      "repo_skill": true,
      "source_type": "repo"
    }
  ]
}
```

### 5.3 Index Entry Fields

| Field | Required | Description |
|-------|----------|-------------|
| `key` | Yes | Path key: `{language}/{author}/{name}` |
| `name` | Yes | Library name |
| `language` | Yes | Programming language |
| `tier` | Yes | `tier1` or `tier2` |
| `group` | Yes | `main` or `contrib` |
| `repo_source_url` | No | URL to the library's repository (for `.libskills/` discovery) |
| `repo_skill` | No | `true` if the skill originates from the repo |
| `source_type` | No | `repo` (from `.libskills/`), `registry` (registry-only), or `mirror` |
| `version` | No | Library version |
| `trust_score` | No | 0–100 |
| `tags` | No | Search tags |
| `summary` | No | One-line description |

### 5.4 Discovery Sources

The aggregation registry discovers skills via:

1. **GitHub code search**: `path:.libskills/skill.json` — finds repositories with self-hosted skills
2. **GitHub topic**: Repositories tagged with `libskills` topic
3. **Manual submission**: PRs to `libskills-registry` adding entries to `index.json`
4. **Future**: Package manager integration (crates.io, PyPI, npm) — auto-discover popular libraries without skills

### 5.5 Distribution

The aggregation index is distributed as a **snapshot** (`registry.zip`), not a git clone. The CLI downloads and caches this snapshot.

- Snapshot URL: `https://github.com/LibSkills/registry/releases/latest/download/registry.zip`
- Update via: `libskills update`

---

## 6. CLI Protocol

### 6.1 Commands

| Command | Phase | Description |
|---------|-------|-------------|
| `init` | Phase 2 | Scaffold a `.libskills/` directory in the current repo |
| `validate <path>` | Phase 2 | Validate a skill against schema |
| `lint <path>` | Phase 2 | Quality checks (token count, required files, completeness) |
| `search <keyword>` | Phase 3 | Fuzzy search registry index by name, tags, summary |
| `get <path>` | Phase 3 | Download skill to local cache |
| `info <path>` | Phase 3 | Show skill metadata |
| `update` | Phase 3 | Refresh local registry index |
| `list` | Phase 3 | List locally cached skills |
| `cache` | Phase 3 | Manage local cache (prune, clear) |
| `find <intent>` | Future | Semantic/vector search |
| `serve` | Future | Start MCP/HTTP API |

### 6.2 Local Cache Path

| Platform | Path |
|----------|------|
| Linux/macOS | `~/.libskills/` |
| Windows | `%APPDATA%/libskills/` |

```
~/.libskills/
├── cache/           # Downloaded skills
├── index.json       # Local index snapshot
└── config.toml      # CLI configuration
```

### 6.3 AI Reading Protocol

An AI agent consumes a skill using the **priority-based protocol** defined in §4.1:

**Phase 1 — Mandatory (P0):**
1. `skill.json` — metadata, version, trust score, risk_level, read_order
2. `overview.md` — what the library is and when to use it
3. `pitfalls.md` — what NOT to do (highest-value file)
4. `safety.md` — red lines and risk constraints

After Phase 1, the AI has enough context to generate correct code.

**Phase 2 — Conditional (P1):**
5. `lifecycle.md` — init/shutdown ordering (if managing lifecycle)
6. `threading.md` — concurrency model (if multi-threaded)
7. `best-practices.md` — recommended patterns (on request)

**Phase 3 — On-demand (P2/P3):**
8. `performance.md` — throughput, latency (when optimizing)
9. `examples/` — reference code snippets

This phased reading avoids wasting context tokens on knowledge that isn't needed for the current task.

---

## 7. Semantic Search (Future)

### 7.1 Embedding Index

Each skill's content can be embedded for semantic search:

```
libskills find "high performance async logging"
→ cpp/gabime/spdlog (score: 0.92)
→ cpp/odyg/quill    (score: 0.85)
→ cpp/ms-gys/sinks  (score: 0.61)
```

### 7.2 Local Embedding Cache

```
~/.libskills/embeddings/
├── index.faiss
└── id_map.json
```

---

## 8. MCP / HTTP API (Future)

```bash
libskills serve --port 8701
```

### Endpoints

```
GET  /v1/skills                           # List all skills
GET  /v1/skills/{language}/{author}/{name} # Get full skill
GET  /v1/skills/.../{section}             # Get specific section (e.g., pitfalls)
GET  /v1/search?q={keyword}               # Search
GET  /v1/find?q={intent}                  # Semantic search
POST /v1/skills                           # Submit a skill (Tier 2)
GET  /health                              # Health check
```

---

## 9. Skill Inheritance (Future)

Skills can inherit knowledge from parent skills to avoid duplication.

```
react-router@6.20
  inherits: react@18
```

When AI reads `react-router`, it also loads `react`'s knowledge first, then applies react-router-specific overrides.

Common chains:
- `react-router` → `react`
- `redux-toolkit` → `redux` → `react`
- `grpc` (multi-language bindings) → base `grpc` core

If library A depends on library B, AI SHOULD load B's skill before A's. The `dependencies.skills` field declares these relationships.

---

## 10. Skill Types

Every skill declares its type to help AI choose the right consumption strategy.

| Type | Example | AI Strategy |
|------|---------|-------------|
| `library` | spdlog, fmt | Load full API + pitfalls |
| `framework` | React, FastAPI | Load lifecycle + routing patterns |
| `sdk` | AWS SDK, Stripe | Load auth + error handling |
| `runtime` | Node.js, Deno | Load event loop + async patterns |
| `tooling` | CMake, Docker | Load configuration patterns |
| `middleware` | Express middleware | Load chain pattern |
| `database` | PostgreSQL driver | Load connection + query patterns |
| `network` | Boost.Asio, libcurl | Load async + error handling |
| `ui` | Dear ImGui, Qt | Load event loop + rendering patterns |
| `compiler` | Clang plugins | Load plugin lifecycle |

---

## 11. Skill Generator (Future)

```bash
libskills generate cpp/gabime/spdlog
```

Given a README, docs, and tests, automatically generate a skill scaffold.

---

## 12. Skill Linting (Future)

```bash
libskills lint cpp/gabime/spdlog
```

Checks:
- Missing required files (`overview.md`, `pitfalls.md`, `safety.md`, at least 1 example)
- Token count outside 500–1500 range per file
- `pitfalls.md` has fewer than 3 entries
- `safety.md` has fewer than 2 entries
- Missing tag entries
- Outdated version field

---

## 13. Completeness Score

Automatically calculated based on file presence:

| Files Present | Score |
|--------------|-------|
| All P0 + P1 + P2 + examples | 100 |
| All P0 + P1 + examples | 80–95 |
| All P0 + examples | 60–75 |
| P0 only | 40–55 |
| Missing P0 files | < 40 |

Included in `skill.json` as `completeness`.

---

## 14. Compatibility Graph

```jsonc
{
  "compatibility": {
    "c++": ["17", "20", "23"],
    "compilers": ["clang>=16", "gcc>=11", "msvc>=2022"],
    "platforms": ["linux-x64", "macos-arm64", "windows-x64"]
  }
}
```

AI uses this to avoid suggesting incompatible compiler flags or platform-specific APIs.

---

## 15. Benchmark Data (Future)

Optional benchmark section in `performance.md`:

```markdown
## Benchmarks

| Config | Throughput | Latency p50 | Latency p99 |
|--------|-----------|-------------|-------------|
| Sync, single thread | 500k/s | 2µs | 10µs |
| Async, 4 threads | 2M/s | 0.5µs | 5µs |
| Flush every log | 10k/s | 100µs | 500µs |
```

---

## 16. Community Ratings (Future)

```jsonc
{
  "community_rating": {
    "reliability": 4.5,
    "hallucination_safety": 4.8,
    "thoroughness": 4.2
  },
  "votes": 128
}
```

---

## 17. Validation Rules

All skills MUST pass these rules:

- `schema` must be `libskills/v1`
- `overview.md` is REQUIRED (P0)
- `pitfalls.md` is REQUIRED (P0), minimum 3 entries
- `safety.md` is REQUIRED (P0), minimum 2 entries
- At least 1 example in `examples/`
- Each markdown file: 500–1500 tokens
- `trust_score`: integer 0–100
- `risk_level`: `high`, `medium`, or `low`
- `read_order`: must contain only P0 file paths
- `skill_version`: must follow semver (`\d+\.\d+\.\d+`)
- File names: lowercase, `.md` extension
- `tags`: minimum 1 tag
- `repo_skill`: must be `true` if skill lives in the library's own repository

---

## 18. Ecosystem Extensions

Language ecosystems MAY extend the LibSkills standard with additional fields. Extensions MUST use namespaced keys to avoid collisions.

### 18.1 Extension Format

```jsonc
{
  "extensions": {
    "crates_io": {
      "crate_name": "serde",
      "features": ["derive", "std", "rc"],
      "min_rust_version": "1.56"
    },
    "pypi": {
      "package_name": "requests",
      "python_requires": ">=3.8",
      "classifiers": ["Intended Audience :: Developers"]
    },
    "npm": {
      "package_name": "react",
      "types": true,
      "side_effects": false
    }
  }
}
```

### 18.2 Reserved Extension Namespaces

| Namespace | Purpose |
|-----------|---------|
| `crates_io` | Rust crate metadata |
| `pypi` | Python package metadata |
| `npm` | Node.js package metadata |
| `conan` | C/C++ package metadata |
| `maven` | Java/Maven metadata |
| `go_mod` | Go module metadata |

### 18.3 Extension Rules

- Extensions MUST NOT contradict the base standard
- Extensions MUST NOT add required fields (only optional enrichment)
- Tools MUST ignore unknown extension namespaces
- Extension authors SHOULD submit namespaces to this spec for reservation

---

## 19. Versioning

### 19.1 Specification Versions

The LibSkills Specification follows **Semantic Versioning**:

| Version | Status | Date | Key Changes |
|---------|--------|------|-------------|
| **v1.0** | Stable | 2026-04-28 | Frozen standard. `.libskills/` convention, P0/P1/P2/P3 priority, `repo_skill`, content index |
| v0.1 | Draft | 2026-04-27 | Initial draft. Schema, registry, AI reading protocol |

### 19.2 Schema Version Compatibility

The `schema` field in `skill.json` declares which version of the spec the skill conforms to (`libskills/v1`).

Tools MUST:
- Validate against the schema version declared in the skill
- Accept skills with newer **minor** schema versions (forward-compatible by ignoring unknown fields)
- Reject skills with newer **major** schema versions (incompatible changes)

### 19.3 What Triggers a Major Version Bump

- Removing a required field
- Changing the semantics of an existing field
- Changing the `.libskills/` directory structure
- Removing a file priority level (P0/P1/P2/P3)

### 19.4 What Does NOT Trigger a Major Version Bump

- Adding new optional fields to `skill.json`
- Adding new knowledge file categories
- Adding new skill types
- Adding new languages

---

## 20. Conformance

A tool, registry, or library is **LibSkills v1.0 conformant** if it meets these requirements:

### 20.1 Skill Files

A skill file is conformant if it:
- Passes schema validation against `libskills/v1`
- Contains all required P0 files (`overview.md`, `pitfalls.md`, `safety.md`)
- Has at least one example file
- All markdown files are 500–1500 tokens
- Follows the `.libskills/` directory structure

### 20.2 CLIs and Tools

A tool is conformant if it:
- Can validate a skill against the schema
- Can read `skill.json` and follow the `read_order` field
- Can discover skills from a library repository's `.libskills/` directory
- Errors on malformed skills with clear diagnostic messages

### 20.3 Aggregation Registries

A registry is conformant if it:
- Indexes skills by `{language}/{author}/{name}`
- Distinguishes `repo_skill` (self-hosted) from registry-only skills
- Provides `repo_source_url` for upstream discovery
- Updates the index periodically (at least weekly)

### 20.4 AI Agents

An AI agent is conformant if it:
- Reads P0 files before generating any code
- Reads P1 files when the generated code uses the relevant feature
- Validates generated code against `pitfalls.md` and `safety.md` constraints
- Respects `risk_level` in consumption priority
