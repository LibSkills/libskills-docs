# Quickstart

Get LibSkills running in 5 minutes.

## 1. Install the CLI

```bash
git clone https://github.com/LibSkills/libskills-cli
cd libskills-cli
cargo build --release
cp target/release/libskills /usr/local/bin/
```

## 2. Create Your First Skill

```bash
# Scaffold a .libskills/ directory in your library repo
cd your-library/
libskills init --name mylib --repo you/mylib --language python --tags "example,testing"
```

This creates:

```
.libskills/
├── skill.json
├── overview.md
├── pitfalls.md
├── safety.md
├── lifecycle.md
├── threading.md
├── best-practices.md
├── performance.md
└── examples/
    └── basic.py
```

## 3. Fill in the Knowledge

Open each `.md` file and replace the placeholders with real knowledge about your library. Focus on:

- **pitfalls.md**: What NOT to do (the most important file)
- **safety.md**: Red lines that must never be crossed
- **overview.md**: What your library is, when to use it

## 4. Validate

```bash
# Check schema compliance
libskills validate .libskills/

# Check quality (token counts, required sections)
libskills lint .libskills/
```

If lint finds issues, run `libskills lint --fix` to auto-repair.

## 5. Commit

```bash
git add .libskills/
git commit -m "Add LibSkills skill file"
git push
```

## 6. List in the Registry

If you want your skill to appear in `libskills search`, add the `libskills` topic to your GitHub repo and update the registry:

```bash
libskills update
libskills search mylib
```

## Using Existing Skills

```bash
# Update the registry index
libskills update

# Find a skill
libskills find "async HTTP client"

# Download a skill
libskills get python/psf/requests

# View metadata
libskills info python/psf/requests
```

## Using the HTTP API

```bash
libskills serve --port 8701

# In another terminal:
curl http://localhost:8701/v1/skills
curl http://localhost:8701/v1/skills/python/psf/requests
curl http://localhost:8701/v1/skills/python/psf/requests/pitfalls.md
curl "http://localhost:8701/v1/search?q=http"
curl "http://localhost:8701/v1/find?q=async+HTTP"
```

## AI Reading Protocol

AI agents consume skills in priority order:

| Phase | Priority | Files | When |
|-------|----------|-------|------|
| 1 | P0 | `overview.md`, `pitfalls.md`, `safety.md` | Before generating any code |
| 2 | P1 | `lifecycle.md`, `threading.md`, `best-practices.md` | When using relevant features |
| 3 | P2/P3 | `performance.md`, `examples/` | On demand |

The P0 files answer: *"What must I know to use this library safely?"*

## Next Steps

- Read [Skill Anatomy](../concepts/skill-anatomy.md) to understand the file structure
- Read [Authoring Guide](authoring.md) to write high-quality skills
- Read [CLI Reference](../reference/cli-commands.md) for all commands
