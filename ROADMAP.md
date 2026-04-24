# LibSkills Roadmap

**Rome wasn't built in a day. Neither is a knowledge layer.**

---

## Phase 0: Foundation — The Minimal Viable Protocol

**Goal:** One repo, one schema, one CLI, one skill. Prove the concept.

### Deliverables

| Item | Description | Status |
|------|-------------|--------|
| **skill schema v1** | A single JSON that defines what a skill is | ✅ Done |
| **specification** | SPEC.md describing the protocol | ✅ Done |
| **constitution** | PHILOSOPHY.md — the seven rules and boundaries | ✅ Done |
| **CLI: `search`** | `libskills search cpp logging` — fuzzy match over registry index | 🚧 |
| **CLI: `get`** | `libskills get cpp/gabime/spdlog` — download to local cache | 🚧 |
| **CLI: `info`** | `libskills info spdlog` — show skill metadata | 🚧 |
| **CLI: `update`** | `libskills update` — refresh registry index | 🚧 |
| **first skill** | spdlog — complete, high-quality reference skill | 🚧 |
| **validation** | Compare AI code quality *with* vs *without* skills | 📅 |
| **registry** | Add 2-3 more core skills (fmt, nlohmann_json) | 📅 |

### Explicitly NOT in Phase 0

- Embedding / vector search
- AI-generated skills
- MCP / HTTP protocol server
- Semantic search
- Ranking / trust engine
- Distributed resolver
- Plugin system
- Authentication / accounts
- GUI of any kind
- Multi-registry support

**Phase 0 constraint:** Only `filesystem + JSON + markdown + cache`.
If only one person maintains it, it must still work.

---

## Phase 1: Community — Make It Usable by Others

**Goal:** People can contribute skills and rely on the CLI in real projects.

### Deliverables

| Item | Description |
|------|-------------|
| **`libskills list`** | List cached skills with metadata |
| **`libskills doctor`** | Validate a locally cached skill against the schema |
| **`libskills init`** | Generate a skill template for contributors |
| **Community review** | Add a CONTRIBUTING.md review process for Tier 1 |
| **Tier 2 support** | Allow community PRs without strict review |
| **Skill inheritance** | redis-py → redis; basic `requires` field |
| **Global index** | `index.json` with all skills for fast search |
| **CI integrity check** | GitHub Action validating PRs against schema |
| **5-10 more skills** | Core C++ + Rust + Python libraries |
| **Publish `cargo install`** | CLI on crates.io |

### Key metric
**Can a new contributor submit a Tier 2 skill in under 15 minutes?**

---

## Phase 2: Discovery — Make Skills Findable

**Goal:** AI agents and IDEs can discover skills automatically.

| Item | Description |
|------|-------------|
| **`libskills find`** | Semantic search / intent matching |
| **`libskills discover`** | Auto-scan Cargo.toml / CMakeLists.txt / requirements.txt |
| **`libskills cache`** | Manage local cache (clear, prune, refresh) |
| **`libskills serve` (MCP)** | Expose skills via Model Context Protocol |
| **IDE integration** | VS Code / JetBrains plugin |
| **Skill auto-load** | Detect `import spdlog` → preload skill |
| **Repository `.libskills/`** | Authors can ship skills in their own repos |
| **Private registry** | Enterprise `~/.libskills/private/` support |
| **30+ skills** | Expanded registry across languages |

### Key metric
**How much does first-compile success rate improve with skill preloading?**

---

## Phase 3: Intelligence — Close the Feedback Loop

**Goal:** Skills improve themselves based on real usage data.

| Item | Description |
|------|-------------|
| **Skill usage analytics** | Which skills reduce errors most? |
| **Cross-language primitives** | `async_runtime_shutdown`, `ownership_transfer` etc. |
| **Skill graph** | spdlog → fmt → allocator — auto-load dependency skills |
| **Skill packs** | `backend-pack` → postgres + redis + grpc + logging |
| **Skill timeline** | Track best practices across library versions |
| **Community rankings** | Stars, votes, freshness, issue health |
| **1000+ skills** | Broad coverage across ecosystems |

### Key metric
**Library integration time: how fast can an AI use a new library?**

---

## Phase 4: Protocol — Become the Standard

**Goal:** LibSkills is the de facto way to package library knowledge.

| Item | Description |
|------|-------------|
| **Enterprise private registries** | Self-hosted skill servers |
| **Global resolver network** | DNS-like discovery across registries |
| **Skill confidence scoring** | Consensus across multiple sources |
| **Skill DSL** | Machine-readable constraint language |
| **Knowledge primitives** | Reusable patterns across languages |
| **Standard adoption** | IDE built-in support, CI integrations |

### Key metric
**"How many libraries have skills?" → "How many *don't*?"**

---

## The KISS Commitment

**Every feature in every phase must pass this test:**

> "Is this directly accelerating the core loop?"
>
> `Discover → Get → Read → Reduce errors`

**No feature is added before pain proves it needs to exist.**

Complexity is not earned by ambition. It is earned by proof.
