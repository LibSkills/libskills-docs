# LibSkills Ecosystem Extensions

Language ecosystems can extend the LibSkills standard with additional metadata via the `extensions` field in `skill.json`.

## Extension Namespace Convention

```
{ecosystem_name}
```

All extension namespaces are flat strings under `extensions`. No nesting of ecosystem metadata within other extensions.

## Schema

```jsonc
{
  // ... base skill.json fields ...
  "extensions": {
    // Ecosystem-specific metadata not covered by the base standard
  }
}
```

## Reserved Namespaces

| Namespace | Ecosystem | Key Fields |
|-----------|-----------|------------|
| `crates_io` | Rust | `crate_name`, `features`, `min_rust_version`, `categories` |
| `pypi` | Python | `package_name`, `python_requires`, `classifiers`, `requires_dist` |
| `npm` | Node.js | `package_name`, `types`, `side_effects`, `engines` |
| `conan` | C/C++ | `package_name`, `settings`, `options`, `requires` |
| `maven` | Java | `group_id`, `artifact_id`, `scm` |
| `go_mod` | Go | `module_path`, `go_version`, `require` |
| `vcpkg` | C/C++ | `port_name`, `port_version`, `features`, `dependencies` |
| `homebrew` | macOS | `formula_name`, `depends_on`, `conflicts_with` |
| `winget` | Windows | `package_id`, `installer_type` |

## Example: Rust / Crates.io

```jsonc
{
  "extensions": {
    "crates_io": {
      "crate_name": "serde",
      "features": ["derive", "std", "rc", "alloc"],
      "default_features": ["std"],
      "min_rust_version": "1.56",
      "categories": ["encoding", "no-std", "parsing"],
      "keywords": ["serialization", "json", "serde"]
    }
  }
}
```

## Example: Python / PyPI

```jsonc
{
  "extensions": {
    "pypi": {
      "package_name": "requests",
      "python_requires": ">=3.8",
      "classifiers": [
        "Development Status :: 5 - Production/Stable",
        "Intended Audience :: Developers",
        "License :: OSI Approved :: Apache Software License"
      ],
      "requires_dist": ["urllib3>=1.21.1", "certifi>=2017.4.17"]
    }
  }
}
```

## How to Reserve a New Namespace

1. Open a PR to this file adding your namespace to the reserved list
2. Describe the ecosystem and key metadata fields
3. Provide at least one example skill that uses the extension
4. Two maintainer approvals required

## Tool Behavior

- Tools MUST ignore unknown extension namespaces
- Tools MAY use extension metadata to enrich search/discovery (e.g., filter by crates.io features)
- Tools MUST NOT fail validation due to extension content (extensions are optional enrichment)
- The `extensions` field itself is always optional

## Extension Field Rules

- Extension fields MUST NOT duplicate information already in the base `skill.json` (e.g., `name`, `version`, `tags`)
- Extension fields SHOULD add ecosystem-specific metadata not covered by the base standard
- If a field has the same name as a base field, the extension field MUST NOT contradict it
