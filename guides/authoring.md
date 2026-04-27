# Authoring Guide

How to write a high-quality LibSkills skill.

## The Golden Rule

> **Write for an AI agent, not a human.**

AIs can read API docs. What they CANNOT do is know which APIs are dangerous, which combinations crash, and which hidden constraints exist. That's what you put in the skill.

## Before You Start

1. Know the library well — you can't explain pitfalls you haven't experienced
2. Review the library's issue tracker for common user mistakes
3. Look at StackOverflow answers tagged with the library

## Writing Order

Write in this order — each file builds on the previous:

1. **overview.md** — Set context
2. **pitfalls.md** — Catalog what goes wrong (most important)
3. **safety.md** — Define absolute red lines
4. **lifecycle.md** — Document init/shutdown
5. **threading.md** — Document concurrency
6. **best-practices.md** — Recommend patterns
7. **performance.md** — Document perf characteristics
8. **examples/** — Working code

## File-by-File Guidelines

### overview.md — 500–800 tokens

Answer these questions:
- What is this library? (1 sentence)
- When should I use it?
- When should I NOT use it?
- What are the 3 most important things to know?

```markdown
# spdlog — Overview

**spdlog** is a fast C++ logging library. Supports sync/async, multiple sinks.

## When to Use
- Any C++ app needing structured logging
- High-throughput scenarios (>1M logs/sec)

## When NOT to Use
- Ultra-low-latency real-time (blocking sync mode)
- Multi-process apps without careful config
```

### pitfalls.md — 800–1500 tokens

The most important file. Minimum 3 entries. Each entry must:

1. State the pitfall clearly ("Do NOT ...")
2. Show a BAD code example
3. Show a GOOD code example
4. Explain WHY the bad example is dangerous

```markdown
### Do NOT use std::endl

// BAD: flushes on every write
logger->info("Value: " + std::to_string(x) + "\n");

// GOOD: spdlog handles buffering
logger->info("Value: {}", x);
```

### safety.md — 500–1000 tokens

Red lines. Minimum 2 entries. Must use the word **NEVER**. These are the hardest constraints — violations cause crashes, data loss, or security vulnerabilities.

```markdown
## NEVER use a logger after fork() without recreating

// fork() duplicates all loggers. The child's copies are invalid.
// Always drop_all() and recreate loggers in the child process.
```

### lifecycle.md — 500–800 tokens

Document:
- How to initialize the library
- How to shut it down correctly
- What happens if you get the order wrong
- Any `atexit`/`Drop`/destructor concerns

### threading.md — 500–800 tokens

Document:
- Is the library thread-safe? (yes/no/partially)
- Which APIs are safe from multiple threads?
- Which APIs require external synchronization?
- Async/event-loop models

### best-practices.md — 500–1000 tokens

Recommended patterns, NOT mandatory constraints. If a pattern being wrong causes a crash, it belongs in pitfalls.md instead.

### performance.md — 500–800 tokens

- Approximate throughput numbers
- Latency characteristics
- Memory footprint
- Bottleneck identification

### examples/ — at least 1 file

- Self-contained and compilable
- Demonstrates 3–5 key APIs
- Uses best practices from the skill
- Under 50 lines

## Writing Style

**DO:**
- Be precise — "will crash" not "might crash"
- Show code — every pitfall has BAD/GOOD examples
- Use imperative — "NEVER", "Do NOT"
- Focus on the 20% of APIs used 80% of the time

**DO NOT:**
- Copy API reference documentation
- Include general programming tutorials
- Use vague language ("may", "might", "sometimes")
- Exceed 1500 tokens per file

## Validation

Before submitting, run:

```bash
libskills validate .libskills/   # Schema check
libskills lint .libskills/       # Quality check
libskills lint --fix .libskills/ # Auto-repair
```
