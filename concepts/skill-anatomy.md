# Skill Anatomy

Every LibSkills skill is a collection of files in a `.libskills/` directory. This document explains the structure, conventions, and design rationale.

## Directory Structure

```
.libskills/
├── skill.json               # Metadata (required)
├── overview.md              # P0 — Library overview (required)
├── pitfalls.md              # P0 — Common mistakes (required, ≥3 entries)
├── safety.md                # P0 — Red lines (required, ≥2 entries)
├── lifecycle.md             # P1 — Init/shutdown
├── threading.md             # P1 — Concurrency model
├── best-practices.md        # P1 — Recommended patterns
├── performance.md           # P2 — Throughput and latency
└── examples/                # P3 — Working code (≥1 example)
    └── basic.{cpp,rs,py,go,js}
```

## File Priority System

Each file has a priority level (P0–P3) that determines when an AI agent should read it.

```
P0: MANDATORY  — Read before generating any code
P1: CONDITIONAL — Read when the generated code uses the feature
P2: ON-DEMAND  — Read when the user requests optimization
P3: REFERENCE  — Read for usage examples
```

### Priority Tests

- **P0 test**: *"If skipped, the AI might produce code that crashes, leaks, or silently corrupts data."*
- **P1 test**: *"If skipped, the AI might produce correct but suboptimal code."*
- P2 and P3 are entirely on-demand — the AI decides when to read them.

## skill.json — Metadata

The only structured file in a skill. Contains:

| Field | Purpose | Example |
|-------|---------|---------|
| `name` | Library name | `"spdlog"` |
| `repo` | GitHub repository | `"gabime/spdlog"` |
| `language` | Programming language | `"cpp"` |
| `tier` | Quality tier | `"tier1"` |
| `group` | Popularity group | `"main"` |
| `version` | Library version targeted | `"1.14.2"` |
| `skill_version` | Skill content version | `"0.1.0"` |
| `schema` | Schema version | `"libskills/v1"` |
| `skill_type` | Classification | `"library"` |
| `repo_skill` | Self-hosted? | `true` |
| `trust_score` | Trust 0–100 | `95` |
| `risk_level` | AI priority | `"medium"` |
| `tags` | Search tags | `["logging", "async"]` |
| `read_order` | Reading order (P0) | `["overview.md", "pitfalls.md", "safety.md"]` |
| `files` | Files by priority | `{"P0": [...], "P1": [...], ...}` |

## Token Limits

Each markdown file must be **500–1500 tokens**. This constraint exists because:

- LLMs have finite context windows — every token spent on documentation is a token NOT spent on the user's code
- AI agents need independently useful chunks — they may read only `threading.md` without reading `overview.md`
- Dense, actionable knowledge is more valuable than exhaustive reference

## The Two Most Important Files

### pitfalls.md (P0)

The #1 value-add of LibSkills. Contains **what NOT to do**. Every entry should show a BAD example and a GOOD example.

```markdown
### Do NOT call shutdown() in a static destructor

// BAD: undefined behavior
struct Cleaner { ~Cleaner() { spdlog::shutdown(); } };

// GOOD: call in main()
int main() { spdlog::shutdown(); return 0; }
```

### safety.md (P0)

Red lines — conditions that must **NEVER** occur. These are the hard constraints that, if violated, will crash or corrupt data.

```markdown
## NEVER use a logger after fork() without recreating it

// After fork(), the child's loggers share state with the parent.
// Always drop and recreate loggers in the child process.
```

## Self-Hosted vs Registry

A skill is valid whether it exists:

1. **In the library's own repo** (`.libskills/`) — primary, decentralized source
2. **In the aggregation registry** — centralized discovery

The `repo_skill: true` flag distinguishes self-hosted skills from registry-only ones. Self-hosted skills are preferred because they're maintained alongside the code.
