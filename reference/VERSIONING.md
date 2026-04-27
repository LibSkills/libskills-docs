# LibSkills Versioning Policy

## Specification Versioning

The LibSkills Specification follows **Semantic Versioning 2.0.0** (`MAJOR.MINOR.PATCH`).

| Version | Status | Schema | Date |
|---------|--------|--------|------|
| **1.0.0** | Stable | `libskills/v1` | 2026-04-28 |
| 0.1.0 | Draft | `libskills/v0` | 2026-04-27 |

## Schema Versioning

The `schema` field in `skill.json` declares which version of the spec the skill conforms to.

**Forward compatibility rule:** Tools MUST accept skills with the same MAJOR schema version, ignoring unknown fields.

```
skill declares: "libskills/v1"
tool supports:  "libskills/v1" → ✅ Validates
tool supports:  "libskills/v2" → ✅ Accepts (minor bump, ignore unknown fields)
tool supports:  "libskills/v0" → ❌ Rejects (major bump, may have different semantics)
```

## What Triggers a MAJOR Version Bump

- Removing or renaming a required field in `skill.json`
- Changing the `.libskills/` directory structure
- Removing a file priority level (P0/P1/P2/P3)
- Changing the semantics of existing fields
- Changing validation rules to be stricter

## What Does NOT Trigger a MAJOR Version Bump

- Adding new optional fields to `skill.json`
- Adding new knowledge file categories
- Adding new `skill_type` values
- Adding new languages to the enum
- Adding new extension namespaces
- Adding new CLI commands

## skill_version vs Schema Version

| Field | Scope | Example |
|-------|-------|--------|
| `schema` | Spec version the skill conforms to | `"libskills/v1"` |
| `skill_version` | Version of the skill content itself | `"1.2.0"` |

A skill can be improved (fix pitfalls, add examples) without changing the schema version. The `skill_version` tracks the content quality, not the format.

## Tool Versioning

The `libskills` CLI follows the same MAJOR.MINOR.PATCH convention. The CLI version is independent of the spec version — a CLI v0.2.0 can support spec v1.0.

## Deprecation Policy

- Deprecated fields are documented for at least one MAJOR version before removal
- Tools SHOULD emit warnings when encountering deprecated fields
- The `schema` field is never deprecated — it's the foundation of version negotiation
