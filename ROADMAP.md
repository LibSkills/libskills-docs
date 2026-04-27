# LibSkills Roadmap

**Standard-first. Decentralized by default. Registry as accelerator.**

---

## Phase 0 — Standard v0.1 ✅

**Define the standard. No platform, no registry as gatekeeper.**

### Deliverables

| Item | Status |
|------|--------|
| One sentence: what is LibSkills? | ✅ "Behavioral Knowledge Layer for open-source libraries" |
| Boundaries: WHAT_LIBSKILLS_IS_NOT | ✅ PHILOSOPHY.md |
| SPEC.md v0.1 — the definitive standard | ✅ Merged, unified, decentralized |
| `.libskills/` directory convention | ✅ Defined in SPEC.md §2 |
| `skill.json` schema v0.1 | ✅ Skill metadata with priority, risk, repo_skill |
| `index.json` schema v0.1 | ✅ Aggregation index with repo discovery fields |

### Core Design Decisions

- **Standard-first, not registry-first**: Any repo can adopt `.libskills/` without registering anywhere
- **Registry is aggregation, not gatekeeping**: Crawls repos, provides search/cache
- **Schema version**: `libskills/v0` — will stay v0 until validated with real AI consumption

---

## Phase 1 — Example Skills (current)

**Prove the standard works by creating real skills.**

### Three example skills

| Library | Language | Rationale |
|---------|----------|-----------|
| spdlog | C++ | Widely used, many pitfalls (async lifecycle, static destructors, format safety) |
| serde | Rust | De-facto standard, complex derive macros, subtle performance traps |
| requests | Python | Most-used HTTP library, obvious surface but hidden edge cases |

Each skill lives in a `.libskills/` directory, complete with:
- `skill.json` (full metadata)
- `overview.md`, `pitfalls.md`, `safety.md` (P0, required)
- `lifecycle.md`, `threading.md`, `best-practices.md` (P1)
- `performance.md` (P2)
- `examples/` (P3, at least 1 example)

### Also indexed in

- `libskills-registry/index.json` — as aggregation entries with `repo_skill: true`
- Each skill is designed to be self-hosted in the library's own repo as `.libskills/`

### Success criteria

A human reading these skills should be able to answer: "What must an AI agent know to use this library safely?" — and the answer should be clear, concise, and actionable.

---

## Phase 2 — Author Tooling (1-2 weeks)

**Make it trivially easy to create and validate skills.**

### CLI commands

| Command | Purpose |
|---------|---------|
| `libskills init` | Scaffold `.libskills/` in the current repo (interactive: asks language, name, tags) |
| `libskills validate <path>` | Validate a skill against the schema |
| `libskills lint <path>` | Quality checks: token count, required files, P0 completeness |

### Architecture

```
libskills-cli/
├── Cargo.toml
└── src/
    ├── main.rs          # Entry point, argument parsing (clap)
    ├── init.rs          # Skill scaffolding
    ├── validate.rs      # Schema validation (jsonschema)
    └── lint.rs          # Quality checking
```

### Constraint

Pure filesystem operations. No network, no registry dependency. An author must be able to use `init`, `validate`, and `lint` entirely offline.

---

## Phase 3 — Aggregation Registry (1-2 weeks)

**Accelerate discovery without gatekeeping.**

### CLI commands

| Command | Purpose |
|---------|---------|
| `libskills search <keyword>` | Fuzzy search the aggregation index |
| `libskills get <path>[@version]` | Download skill to local cache |
| `libskills info <path>` | Show skill metadata |
| `libskills update` | Refresh local index from aggregation |
| `libskills list` | List locally cached skills |
| `libskills cache` | Manage cache (prune, clear) |

### Aggregation mechanics

1. **GitHub code search** for `path:.libskills/skill.json` — auto-discovers repo-level skills
2. **GitHub topic** `libskills` — repos can opt in to being indexed
3. **Manual PR** — submit entries directly to `index.json`
4. **GitHub Actions** — periodic crawl, regenerate `index.json`, publish release snapshot

### Local cache

```
~/.libskills/
├── cache/           # Downloaded skills (by key)
├── index.json       # Local index snapshot
└── config.toml      # CLI config
```

---

## Phase 4 — Validation Experiments (2-3 weeks)

**Prove skills reduce AI errors. This is the most critical phase.**

### Experiment design

- **Control**: AI generates code for spdlog/serde/requests without skills
- **Treatment**: AI reads the `.libskills/` skill first, then generates
- **Metrics**: hallucination rate, first-compile rate, runtime error count, token cost, iteration count
- **Task suite**: 5-10 standard tasks per library (read config, handle errors, async setup, etc.)

### Output

First batch of real evidence. If skills demonstrably reduce errors, proceed to Phase 5. If not, revisit the standard.

---

## Phase 5 — Expand Skills (1-2 months)

**Build trust through quality, not quantity.**

Target: 10-20 high-quality skills across C++, Rust, and Python.

| Language | First batch |
|----------|-------------|
| C++ | spdlog, fmt, nlohmann/json, asio |
| Rust | serde, tokio, reqwest, clap |
| Python | requests, fastapi, pydantic, pytest |

### Priority heuristic

Choose libraries that are:
1. Widely used (high AI encounter rate)
2. Dense with pitfalls (high hallucination potential)
3. Under-documented in the behavior layer (high marginal value)

---

## Phase 6 — Community Tooling ✅

**Lower the barrier for skill authors.**

- `libskills lint` auto-fixes (suggest missing sections)
- GitHub Action: validate `.libskills/` on PR
- Badge: "LibSkills Ready" shield for READMEs
- Tier 2 community submissions with lightweight review

### Adoption flywheel

```
Author adds .libskills/ → GitHub Action validates → Badge appears in README
→ Aggregation index discovers it → libskills search finds it
→ AI agents consume it → Errors drop → More authors adopt
```

---

## Phase 7 — Semantic Search ✅

**Only when plain text search is genuinely insufficient.**

- `libskills find "async logger with rotation"`
- Embeddings + FAISS + ranking + trust score
- Pre-computed embeddings distributed with the aggregation index

---

## Phase 8 — Protocol Formalization ✅

**The standard is frozen as v1.0.**

- ✅ LibSkills Spec v1.0 as the definitive standard
- ✅ `schema: "libskills/v1"` — frozen data contract
- ✅ VERSIONING.md — semantic versioning policy
- ✅ EXTENSIONS.md — ecosystem extension mechanism
- ✅ Conformance criteria for tools, registries, AI agents
- ✅ "Any repository can carry a skill" — `.libskills/` is the canonical convention

### What "frozen" means

- `libskills/v1` schema will not change in backwards-incompatible ways
- New optional fields may be added in minor versions
- Breaking changes require `libskills/v2` (not planned)
- Tools can rely on v1 stability for integration

---

## Phase 9 — AI Integration (future)

- MCP server: `libskills serve`
- IDE plugins (VS Code, JetBrains)
- CI checks: "You're using spdlog — read its skill first"
- Agent-native knowledge loading

---

## Phase 10 — Ecosystem (long-term)

- Package manager integration (auto-discover libraries without skills)
- Enterprise private registries
- Skill marketplace / bounty system
- Cross-skill dependency graphs

---

## The KISS Law

**Every feature must pass this test:**

> Does this directly accelerate the core loop?
>
> `Discover → Read → Reduce errors`

**Never add a feature before pain proves it needs to exist.**

### The right order

```
1. Define the standard    ← Phase 0 ✅
2. Create example skills  ← Phase 1 ✅
3. Build author tooling   ← Phase 2 ✅
4. Aggregate discovery    ← Phase 3 ✅
5. Prove value            ← Phase 4 (next)
6. Expand ecosystem       ← Phase 5-6 ✅
7. Semantic search        ← Phase 7 ✅
8. Protocolize            ← Phase 8 ✅
9. Platformize            ← Phase 9-10
```

### Guardrails

- If tomorrow only you maintain it, does it still work? If not, it's too complex.
- The `.libskills/` convention must work with zero infrastructure.
- All complexity must be *proven necessary*, not "might be needed later."
- When in doubt: do the next phase. Not the future.
