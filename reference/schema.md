# Schema Reference

Technical reference for `skill.json` and `index.json` schemas.

## skill.json

The skill metadata file. Validated against [skill.json schema](https://github.com/LibSkills/libskills-schema/blob/main/schemas/skill.json).

### Required Fields

| Field | Type | Constraints |
|-------|------|-------------|
| `name` | string | Library name |
| `repo` | string | GitHub repository (`author/name`) |
| `language` | string | Enum: `cpp`, `rust`, `python`, `go`, `js` |
| `tier` | string | Enum: `tier1`, `tier2` |
| `group` | string | Enum: `main`, `contrib` |
| `version` | string | Library version targeted |
| `skill_version` | string | Semver: `\d+\.\d+\.\d+` |
| `schema` | string | Schema version: `libskills/v1` |
| `skill_type` | string | Enum: `library`, `framework`, `sdk`, `runtime`, `tooling`, `middleware`, `database`, `network`, `ui`, `compiler` |
| `repo_skill` | boolean | `true` if in library's own repo |
| `trust_score` | integer | 0–100 |
| `updated_at` | string | ISO 8601 date-time |
| `tags` | string[] | MinItems: 1 |
| `read_order` | string[] | P0 file paths in reading order |
| `files` | object | Keyed by `P0`, `P1`, `P2`, `P3` |
| `risk_level` | string | Enum: `high`, `medium`, `low` |

### Optional Fields

| Field | Type | Description |
|-------|------|-------------|
| `verified` | boolean | Passed review |
| `official` | boolean | Maintainer-authored |
| `completeness` | integer | 0–100, auto-calculated |
| `compatibility` | object | Language versions, compilers, platforms |
| `trust_score_sources` | object | Score breakdown (official_review, stars, community_votes, update_freshness, issue_health) |
| `dependencies` | object | `required`, `optional`, `skills` arrays |
| `inherits` | string \| null | Parent skill key |
| `extensions` | object | Ecosystem metadata |
| `community_rating` | object | reliability, hallucination_safety, thoroughness |

### files Object

```jsonc
{
  "files": {
    "P0": ["overview.md", "pitfalls.md", "safety.md"],
    "P1": ["lifecycle.md", "threading.md", "best-practices.md"],
    "P2": ["performance.md"],
    "P3": ["examples/basic.cpp"]
  }
}
```

### compatibility Object

```jsonc
{
  "compatibility": {
    "c++": ["17", "20", "23"],
    "rust": ["1.70", "stable"],
    "python": ["3.10", "3.11", "3.12"],
    "compilers": ["clang>=16", "gcc>=11"],
    "platforms": ["linux-x64", "macos-arm64", "windows-x64"]
  }
}
```

### Extensions Example

```jsonc
{
  "extensions": {
    "crates_io": {
      "crate_name": "serde",
      "features": ["derive", "std"],
      "min_rust_version": "1.56"
    }
  }
}
```

## index.json

The aggregation registry index. Validated against [index.json schema](https://github.com/LibSkills/libskills-schema/blob/main/schemas/index.json).

### Required Fields

| Field | Type | Description |
|-------|------|-------------|
| `schema` | string | `libskills/v1` |
| `version` | integer | Incremented on each update |
| `updated_at` | string | ISO 8601 date-time |
| `skills` | array | Registered skill entries |

### Skills Array Entry

| Field | Required | Type | Description |
|-------|----------|------|-------------|
| `key` | Yes | string | `{lang}/{author}/{name}` |
| `name` | Yes | string | Library name |
| `language` | Yes | string | Programming language |
| `tier` | Yes | string | Quality tier |
| `group` | Yes | string | Popularity group |
| `version` | No | string | Library version |
| `trust_score` | No | integer | 0–100 |
| `tags` | No | string[] | Search tags |
| `summary` | No | string | One-line description |
| `repo_source_url` | No | string | Source repository URL |
| `repo_skill` | No | boolean | Self-hosted skill |
| `source_type` | No | string | `repo`, `registry`, or `mirror` |
| `risk_level` | No | string | `high`, `medium`, `low` |
