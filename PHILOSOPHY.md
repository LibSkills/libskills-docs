# LibSkills Philosophy

**The constitution of the LibSkills project.**

---

## The Core Belief

LibSkills exists for one reason: **to reduce the cost of mistakes in software development.**

Code is getting easier to generate. Understanding is getting more expensive.
The hard part is no longer *writing* — it's *judging, integrating, and knowing the constraints.*

LibSkills is not a knowledge base. It is a **risk perception layer.**

---

## What LibSkills Is

LibSkills is a **Behavioral Knowledge Layer** for open-source libraries.

It answers the question: *"What must an AI agent know to use this library safely?"*

---

## What LibSkills Is NOT

- **Not** a documentation mirror — we don't copy API references
- **Not** a README collector — we don't mirror repos
- **Not** a package manager — we manage *knowledge*, not code
- **Not** an encyclopedia — we focus on high-density experiential knowledge
- **Not** a tutorial platform — we don't teach fundamentals
- **Not** a search engine — we serve pre-compiled, curated knowledge
- **Not** a StackOverflow replacement — we capture behavioral contracts, not opinions
- **Not** an AI copilot — we are the safety layer *under* the copilot

---

## The Filtering Philosophy

The world does not lack information. It lacks **knowing what matters.**

Most knowledge systems assume all information has equal value. It does not.

For spdlog:
- `logger->info("hello")` — unimportant
- Async logger lifecycle ordering — critical
- Flush-before-destroy requirement — critical
- Signal handler unsafety — critical

LibSkills is not a knowledge library. It is an **importance engine.**

---

## The Three Layers

```
Source code     →  What the library *can* do
Documentation   →  How to do it
Skill           →  Where it *will break*
```

This is the clearest model of what LibSkills contributes.

---

## The Seven Rules

### Rule 1: No documentation copying

A skill must never duplicate content that already exists in the library's official documentation. If the user needs API reference, they should read the docs. The skill captures what the docs *don't* say: constraints, pitfalls, lifecycle, hidden behaviors.

### Rule 2: Only high-cost knowledge

Every piece of knowledge in a skill must answer at least one of:
- "Where is this library most likely to crash?"
- "What hidden constraints exist?"
- "What is the most expensive mistake an AI can make?"
- "What is invisible from reading the API signature?"

If a piece of knowledge doesn't reduce error probability, it doesn't belong in the skill.

### Rule 3: Error reduction > knowledge accumulation

A skill with 5 carefully chosen pitfalls is more valuable than a skill with 50 facts. The primary metric: *how many AI mistakes does it prevent?* Not: *how much does it cover?*

### Rule 4: Machine-readable constraints

Every constraint in a skill must be representable in a structured format (`constraints.json`, `lifecycle.json`, `hazards.json`). Free-form prose is supplementary, not primary. AI agents must be able to derive actionable rules without parsing natural language.

### Rule 5: Chunked and partial-read compatible

No file in a skill should exceed 1500 tokens. Every file must be independently useful — an AI agent should be able to read `threading.md` without reading `overview.md`. The reading order is a recommendation, not a requirement.

### Rule 6: Version-bound

Every skill must declare which library version(s) it targets. A skill for spdlog 1.5 is a different artifact from a skill for spdlog 1.14. Skills must include version ranges (`introduced_in`, `deprecated_in`) for API-level knowledge.

### Rule 7: Trust-graded

Every skill must carry a trust score (0-100) and declare its tier (tier1 or tier2), author type (official, community, enterprise, ai_generated), and stability level (experimental, stable, deprecated). AI agents use these signals to decide how much to trust the knowledge.

---

## Documentation vs. Skill

```
Documentation describes.
Skill reminds.
```

Documentation says: "This library can do X."
Skill says: "This is where it will break."

---

## Completeness vs. Correctness

Skill should not pursue completeness. Completeness means redundancy.

Pursue **enough correctness**: the AI doesn't need to know everything. It only needs to avoid critical errors.

---

## Pre-understanding

Traditional AI workflow:
```
Encounter library → Learn on the fly → Guess → Generate
```

LibSkills workflow:
```
Encounter library → Load pre-compiled experience → Generate
```

This is what human expertise looks like: experts don't know more. They know **which parts are dangerous.**

---

## The Goal

LibSkills is **cognitive compression**:
- A human needs hours of reading, debugging, and crashes to learn spdlog's async lifecycle
- A skill compresses that into 1500 tokens

Software engineering's largest cost is not writing code. It is **the cost of understanding.**

---

## Success Metrics

LibSkills measures success by:

1. **AI hallucination rate** — fewer incorrect API calls after skill consumption
2. **First-compile success rate** — code that compiles on the first try
3. **Iteration count** — fewer "fix this" rounds
4. **Integration time** — how fast a new library becomes usable
5. **Skill corpus quality** — completeness threshold across the registry

---

## What This Makes Possible

- **AI agents** that never guess a library API
- **IDEs** that surface constraints during autocomplete
- **CI systems** that check for misuse patterns
- **Enterprise teams** that standardize library usage across thousands of developers
- **A new software layer**: source → package manager → runtime → **knowledge layer** → docs

---

## Skill as Protocol, Not Repository

LibSkills defines a **format and a contract**, not a storage location. The standard is the product.

A skill file is valid whether it lives in:
- The library's own repository (`.libskills/`) — *primary, decentralized source*
- The official LibSkills aggregation registry — *convenience discovery layer*
- An enterprise private registry — *internal use*
- A local directory (`~/.libskills/private/`) — *personal use*

The `.libskills/` directory convention is the foundation. Like `.editorconfig`, `package.json`, or `Dockerfile`, it is a convention any repository can adopt without registration, approval, or gatekeeping.

The aggregation registry exists to **accelerate discovery**, not to **control** distribution. It crawls GitHub repositories for `.libskills/` directories and provides search, caching, and quality signals.

The CLI acts as a **resolver**: given a library name, it discovers the best available skill by checking local cache → aggregation index → upstream repository → mirrors.

LibSkills is a **protocol first, platform second.**

### Why Standard-First

- **Network effects via convention**: If `.libskills/` becomes the standard way to describe library behavior for AI, every AI tool learns to look for it — without a central server
- **Zero-friction adoption**: A library author adds `.libskills/` to their repo. Done. No registration, no PR, no approval
- **Survives the platform**: Even if the LibSkills registry disappears, the standard lives on in every repository that adopted the convention
- **The value is the format, not the database**: npm's value is `package.json`, not registry.npmjs.org. Docker's value is `Dockerfile`, not Docker Hub. LibSkills' value is `skill.json` + `.libskills/`, not the aggregation index
