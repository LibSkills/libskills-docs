# AI Integration Guide

How to integrate LibSkills with AI coding assistants, IDEs, and CI systems.

## Integration Patterns

### 1. Direct File Access (Simplest)

If your AI tool has filesystem access, read `.libskills/` directly:

```python
# Python example
import json
from pathlib import Path

def load_skill(library_path: str) -> dict:
    skill_dir = Path(library_path) / ".libskills"
    with open(skill_dir / "skill.json") as f:
        meta = json.load(f)

    files = {}
    for priority in ["P0", "P1", "P2", "P3"]:
        for filename in meta["files"][priority]:
            filepath = skill_dir / filename
            if filepath.exists():
                with open(filepath) as f:
                    files[filename] = f.read()

    return {"meta": meta, "files": files}
```

### 2. HTTP API

Start the server:

```bash
libskills serve --port 8701
```

Query from your AI tool:

```python
import requests

# Find a skill for a library
r = requests.get("http://localhost:8701/v1/search", params={"q": "spdlog"})
skills = r.json()["results"]

# Get full skill with all file contents
key = skills[0]["key"]  # "cpp/gabime/spdlog"
r = requests.get(f"http://localhost:8701/v1/skills/{key}")
skill = r.json()
# skill["_contents"]["pitfalls.md"] contains the pitfalls text
```

### 3. CLI Invocation

Call the CLI from your tool:

```bash
# Search
libskills search "async logger" --json

# Get full skill as JSON
libskills get cpp/gabime/spdlog --json

# Semantic search
libskills find "fast C++ logging" --json
```

## AI Reading Protocol

When your AI agent prepares to generate code using a library, it MUST follow this protocol:

### Phase 1: P0 — Mandatory (always read)

1. **skill.json** — metadata, version, trust_score, risk_level
2. **overview.md** — what the library is, when to use it
3. **pitfalls.md** — what NOT to do (highest-value file)
4. **safety.md** — red lines, absolute constraints

After Phase 1, the AI has enough context to avoid crashes.

### Phase 2: P1 — Conditional (read when relevant)

5. **lifecycle.md** — if generating init/shutdown code
6. **threading.md** — if generating multi-threaded code
7. **best-practices.md** — if user asks for recommendations

### Phase 3: P2/P3 — On-Demand

8. **performance.md** — if optimizing
9. **examples/** — as reference

### Implementation Pseudocode

```python
def generate_code(library, task):
    skill = load_skill(library)

    # Phase 1: Always read P0
    context = []
    for file in skill["meta"]["read_order"]:
        context.append(skill["_contents"][file])

    # Build system prompt with pitfalls and safety
    system_prompt = f"""You are using {library}.
Before generating code, review these constraints:
{context[1]}  # pitfalls
{context[2]}  # safety
"""

    # Phase 2: Conditional
    if task.uses_threading:
        context.append(skill["_contents"]["threading.md"])
    if task.uses_lifecycle:
        context.append(skill["_contents"]["lifecycle.md"])

    # Generate code with enriched context
    return llm.generate(task, system_prompt=system_prompt, extra_context=context)
```

## CI Integration

Add LibSkills validation to your CI pipeline:

```yaml
# .github/workflows/libskills.yml
name: LibSkills

on:
  pull_request:
    paths: ['.libskills/**']

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions-rs/toolchain@v1
        with: {toolchain: stable}
      - run: cargo install libskills
      - run: libskills validate .libskills/
      - run: libskills lint .libskills/
```

## IDE Plugin Integration (Future)

Planned integration points:
- **Cursor / Copilot**: Auto-load skill context before generating code that imports a library
- **VS Code**: Hover tooltips showing pitfalls when using known-bad patterns
- **JetBrains**: Inspection that flags code violating safety.md constraints
