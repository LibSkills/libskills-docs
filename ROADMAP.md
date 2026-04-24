# LibSkills Roadmap

**From zero to protocol. One step at a time.**

This is not an ideal roadmap. This is a roadmap that will not be crushed by complexity.
Every phase proves value before moving to the next.

---

## Phase 0 — Define the Core (1-3 days)

**No code. Do not write code.**

### Deliverables

| Item | Status |
|------|--------|
| One sentence: what is LibSkills? | ✅ "Behavioral Knowledge Layer for open-source libraries" |
| Boundaries: WHAT_LIBSKILLS_IS_NOT.md | ✅ Covered in PHILOSOPHY.md |
| Minimal schema: skill.json with only required fields | ✅ Defined in SPEC.md |

### What Phase 0 produces

```
philosophy + schema + boundary
```

---

## Phase 1 — Build the First Skill (3-7 days)

**Do not write a CLI. Prove the skill works first.**

### Goal

Build one complete, high-quality skill and verify that it makes AI agents produce better code.

### Recommended first skill

**spdlog** — C++ logging library. Everyone uses it. Many misuse it.

### Directory structure

```
skills/
└── cpp/
    └── gabime/
        └── spdlog/
            ├── skill.json
            ├── overview.md
            ├── pitfalls.md
            ├── lifecycle.md
            ├── threading.md
            └── examples/
```

### Success criteria

Can an AI agent produce correct, safe spdlog code after reading the skill?
If the answer is "yes" — Phase 1 is complete.

---

## Phase 2 — CLI MVP (1-2 weeks)

**Not a full CLI. Only what the core loop needs.**

### Commands

| Command | Purpose | Priority |
|---------|---------|----------|
| `libskills search <keyword>` | Fuzzy search registry index | P0 |
| `libskills get <path>` | Download skill to local cache | P0 |
| `libskills info <path>` | Show skill metadata | P0 |

### Architecture

```
~/.libskills/
├── cache/       # Downloaded skills
├── index.json   # Local index snapshot
└── config.toml  # CLI configuration
```

### Explicitly NOT in Phase 2

- Server / daemon / MCP
- Embedding / vector search
- AI-generated skills
- Registry mirror / distributed registry
- Plugin system
- Authentication
- GUI

### Constraint

```
filesystem + JSON + markdown + cache = all you need
```

**If only one person maintains this, it must still work.**

---

## Phase 3 — Registry Structure (1-2 weeks)

**Organize the skills into a proper repository.**

### Repositories

| Repository | Purpose |
|------------|---------|
| LibSkills/LibSkills | Project root — README linking to sub-repos |
| libskills-schema | JSON Schema definitions |
| libskills-registry | Skill files organized by language/author/name |
| libskills-cli | Rust CLI source |
| libskills-docs | Philosophy, specification, roadmap |

### Registry layout

```
registry/
├── index.json
├── cpp/
│   ├── gabime/
│   │   └── spdlog/
│   └── nlohmann/
│       └── json/
├── rust/
│   └── tokio-rs/
│       └── tokio/
└── python/
    └── psf/
        └── requests/
```

---

## Phase 4 — Real Validation (2-4 weeks)

**This is the most important phase. Prove the thesis.**

### Experiment

**Without skill:** AI generates code using an unfamiliar library.
**With skill:** AI reads the skill first, then generates.

### Metrics to track

| Metric | Description |
|--------|-------------|
| First-compile rate | Code compiles on first attempt |
| Bug count | Runtime errors, crashes, logic bugs |
| Hallucination rate | Incorrect API calls that don't exist |
| Token cost | Tokens consumed to reach correct code |
| Iteration count | Number of "fix this" rounds |
| Integration time | How fast a new library becomes usable |

### Output

First batch of real evidence that skills reduce AI errors.

---

## Phase 5 — Expand Skills (1-2 months)

**Do not expand features. Expand high-quality skills.**

### Languages (first batch)

| Language | Priority |
|----------|----------|
| C++ | spdlog, fmt, asio, nlohmann/json |
| Rust | tokio, serde, reqwest |
| Python | requests, fastapi |

### Output

A trusted Skill Corpus of 10-20 high-quality skills.

---

## Phase 6 — Contribution Experience (1-2 months)

**Lower the barrier for skill authors.**

### New CLI commands

| Command | Purpose |
|---------|---------|
| `libskills init` | Generate skill template |
| `libskills doctor` | Validate skill against schema |

### Tier 2 support

Allow community PRs. Lightweight review process.

---

## Phase 7 — Search Upgrade (only when needed)

**Do not do this until plain search is genuinely insufficient.**

- `libskills find "async logger with rotation"` — semantic / intent search
- Embeddings + ranking + trust score (only if needed)

---

## Phase 8 — Repository Skill Support (only when needed)

**Do not do this until someone asks to ship skills in their own repo.**

- `.libskills/` directory in library repositories
- `libskills resolve <library>` — auto-discover from multiple sources

---

## Phase 9 — Protocol (future)

**Do not do this until multiple clients exist.**

- Formal LibSkills Spec as a standalone standard
- "Any repository can carry a skill" — LibSkills becomes a protocol, not a platform

---

## Phase 10 — AI Integration (future)

- MCP server
- IDE plugins (VS Code, JetBrains)
- CI checks for misuse patterns
- Agent-native knowledge loading

---

## The KISS Law

**Every feature must pass this test:**

> Is this directly accelerating the core loop?
>
> `Discover → Get → Read → Reduce errors`

**Never add a feature before pain proves it needs to exist.**

### The right order

```
1. Prove value          ← You are here
2. Expand ecosystem
3. Protocolize
4. Platformize
```

### Wrong order (trap)

```
Design Phase 9 during Phase 2.
```

### Guardrails

- If tomorrow only you maintain it, does it still work? If not, it's too complex.
- All complexity must be *proven necessary*, not "might be needed later."
- When in doubt: do the next step. Not the future.
