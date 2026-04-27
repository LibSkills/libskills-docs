[![LibSkills](https://img.shields.io/badge/LibSkills-ready-28a745?logo=checkmarx&logoColor=white)](https://github.com/LibSkills)

---

# LibSkills Ready Badge

Add this badge to your README to show your library has a `.libskills/` directory:

**Markdown:**
```markdown
[![LibSkills](https://img.shields.io/badge/LibSkills-ready-28a745?logo=checkmarx&logoColor=white)](https://github.com/LibSkills)
```

**reStructuredText:**
```rst
.. image:: https://img.shields.io/badge/LibSkills-ready-28a745?logo=checkmarx&logoColor=white
   :target: https://github.com/LibSkills
   :alt: LibSkills
```

## Badge Variants

| Badge | Markdown |
|-------|----------|
| Ready (green) | `[![LibSkills](https://img.shields.io/badge/LibSkills-ready-28a745?logo=checkmarx&logoColor=white)](https://github.com/LibSkills)` |
| Tier 1 (blue) | `[![LibSkills](https://img.shields.io/badge/LibSkills-tier1-007bff?logo=checkmarx&logoColor=white)](https://github.com/LibSkills)` |
| Tier 2 (orange) | `[![LibSkills](https://img.shields.io/badge/LibSkills-tier2-ffc107?logo=checkmarx&logoColor=white)](https://github.com/LibSkills)` |

## CI Integration

To automatically validate your `.libskills/` directory on every PR, add this workflow:

```yaml
# .github/workflows/libskills.yml
name: LibSkills

on:
  pull_request:
    paths:
      - '.libskills/**'
  push:
    paths:
      - '.libskills/**'

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup Rust
        uses: actions-rs/toolchain@v1
        with:
          toolchain: stable

      - name: Install libskills
        run: cargo install libskills

      - name: Validate
        run: libskills validate .libskills/

      - name: Lint
        run: libskills lint .libskills/
```
