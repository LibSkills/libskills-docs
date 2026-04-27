# Contributing to LibSkills

There are two ways to contribute:

1. **Add `.libskills/` to your own repo** — the primary path. Self-host your skill. No registration needed.
2. **Submit to the aggregation registry** — if you want your skill to appear in `libskills search`.

---

## Path 1: Add `.libskills/` to Your Repo (Recommended)

This is the primary contribution path. Your skill lives alongside your code.

### Steps

1. Read [SPEC.md](SPEC.md) to understand the standard.
2. Create a `.libskills/` directory at the root of your repository:

   ```
   your-library/
   ├── .libskills/
   │   ├── skill.json            # Required: metadata
   │   ├── overview.md            # Required: P0 — library overview
   │   ├── pitfalls.md            # Required: P0 — what NOT to do (3+ entries)
   │   ├── safety.md              # Required: P0 — red lines (2+ entries)
   │   ├── lifecycle.md           # P1 — init/shutdown
   │   ├── threading.md           # P1 — concurrency
   │   ├── best-practices.md      # P1 — recommended patterns
   │   ├── performance.md         # P2 — perf characteristics
   │   └── examples/
   │       └── basic.{cpp,rs,py,go,js}
   └── src/
   ```

3. Set `"repo_skill": true` in your `skill.json`.
4. Commit and push. That's it — your skill is live.
5. (Optional) Add the `libskills` topic to your GitHub repo to be auto-discovered by the aggregation registry.

### Validating your skill

```bash
# Install the CLI (future)
cargo install libskills

# Validate
libskills validate .libskills/

# Quality check
libskills lint .libskills/
```

---

## Path 2: Submit to the Aggregation Registry

Use this path if you want your skill to appear in `libskills search`.

### Steps

1. Read [SPEC.md](SPEC.md) thoroughly.
2. Fork [libskills-registry](https://github.com/LibSkills/libskills-registry).
3. Add an entry to `index.json`:

   ```jsonc
   {
     "key": "cpp/gabime/spdlog",
     "name": "spdlog",
     "language": "cpp",
     "tier": "tier2",
     "group": "contrib",
     "version": "1.14.2",
     "trust_score": 70,
     "tags": ["logging", "async", "thread-safe"],
     "summary": "Fast C++ logging library with async support",
     "repo_source_url": "https://github.com/gabime/spdlog",
     "repo_skill": true,
     "source_type": "repo"
   }
   ```

4. If your skill exists only in the registry (not in a repo), set `"repo_skill": false` and `"source_type": "registry"`. Include the skill files in the registry repo under the appropriate path.
5. Open a pull request.

---

## Skill Requirements

### Required for ALL submissions

- `skill.json` with complete metadata (see schema)
- `overview.md` — library overview and purpose
- `pitfalls.md` — what NOT to do (at least 3 entries)
- `safety.md` — red lines (at least 2 entries)
- At least one example in `examples/`
- All markdown files between 500–1500 tokens
- `repo_skill`: `true` if in library repo, `false` if registry-only

### Strongly encouraged

- `lifecycle.md` — init/shutdown constraints
- `threading.md` — concurrency guarantees
- `best-practices.md` — recommended patterns
- `performance.md` — perf characteristics

---

## Tier 1 vs Tier 2

| Aspect | Tier 1 | Tier 2 |
|--------|--------|--------|
| Who | LibSkills maintainers | Anyone |
| Review | Full accuracy audit | Format + safety check |
| Trust | 90–100 | 50–89 |
| Update cadence | Within 60 days of release | Best effort |
| Read order | AI reads first | AI falls back if no Tier 1 |

Submit your skill as Tier 2 initially. If it receives community recognition and review, it can be upgraded to Tier 1 via pull request review.

For repo-hosted skills (`.libskills/`), the tier is declared in `skill.json`. The aggregation registry trusts the repository's self-declaration.

---

## Main vs Contrib

**Main**: Libraries that are the de-facto standard in their category (spdlog, tokio, serde, requests, fmt).

**Contrib**: Smaller, niche, or newer libraries. No barriers to entry — any library qualifies.

---

## File Naming

- All directory and file names MUST be **lowercase**
- Use **kebab-case** for multi-word names (e.g., `best-practices.md`)
- File extensions: `.json`, `.md`
- Example file extensions: `.cpp`, `.rs`, `.py`, `.go`, `.js`

---

## Writing Guidelines

### Write for AI agents, not humans

- Be precise and unambiguous
- Include code snippets that compile
- Focus on the 20% of APIs used 80% of the time
- Highlight what CAN go wrong more than what can go right
- Every file must be independently useful (500–1500 tokens)

### DO NOT

- Copy full API reference documentation
- Mirror README content
- Include general programming tutorials
- Use vague language ("may", "might", "sometimes")
- Exceed 1500 tokens per file

---

## Pull Request Process

### For registry submissions

1. Ensure your skill passes schema validation.
2. Ensure all required files exist and are non-empty.
3. Ensure your `index.json` entry is correct.
4. Tier 2 PRs: reviewed within 3–5 business days.
5. Tier 1 PRs: require 2 maintainer approvals.

### For repo-hosted skills

No PR needed. Just add `.libskills/` to your repo and (optionally) add the `libskills` GitHub topic. The aggregation crawler will discover it automatically.

---

## Getting Help

- Open a [GitHub Discussion](https://github.com/LibSkills/LibSkills/discussions)
- Check [SPEC.md](SPEC.md) for format specifics
- Run `libskills validate` to check your skill
