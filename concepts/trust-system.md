# Trust System

LibSkills uses a multi-axis trust system to help AI agents decide how much to trust a skill.

## Axes

### Tier (Quality)

| Tier | Who | Review | Trust Range |
|------|-----|--------|-------------|
| **Tier 1** | LibSkills maintainers | Full accuracy audit | 90–100 |
| **Tier 2** | Community | Format + safety check | 50–89 |

**Upgrading:** Tier 2 → Tier 1 requires 2 maintainer approvals and a full accuracy review.

### Group (Popularity)

| Group | Criteria |
|-------|----------|
| **Main** | 10,000+ GitHub stars OR ecosystem standard OR dependency of 5+ main-group libraries |
| **Contrib** | All other libraries |

### Trust Score (0–100)

Calculated from 5 components:

| Component | Max | Source |
|-----------|-----|--------|
| Official Review | 40 | Tier 1 maintainer review |
| Stars | 20 | GitHub stars tier (10K+ = 20, 5K+ = 15, 1K+ = 10, <1K = 5) |
| Community Votes | 20 | User ratings and usage signals |
| Update Freshness | 15 | Skill updated within 60 days of library release |
| Issue Health | 5 | Low open issue count relative to stars |

**Interpretation:**
| Score | Meaning |
|-------|---------|
| 95–100 | Gold standard — fully verified, actively maintained |
| 80–94 | High quality — reviewed but minor gaps possible |
| 60–79 | Community — useful but not fully audited |
| 0–59 | Draft — minimal validation |

### Risk Level

| Level | Meaning | AI Priority |
|-------|---------|-------------|
| **high** | Misuse causes crashes, data corruption, or security issues | AI MUST read P0 files |
| **medium** | Misuse causes bugs or unexpected behavior | AI SHOULD read P0 files |
| **low** | Misuse causes suboptimal but correct behavior | AI MAY read P0 files |

## repo_skill — Self-Hosted Trust

When `repo_skill: true`, the skill lives in the library's own repository (`.libskills/`). This carries inherent trust:
- The library maintainers endorse the skill
- Updates track library releases naturally
- Content is versioned alongside the code

Registry-only skills (`repo_skill: false`) start at a lower trust baseline.

## For AI Agents

When consuming a skill, AI agents should:

1. **Prefer Tier 1 over Tier 2** — higher review confidence
2. **Respect risk_level** — high-risk libraries require mandatory P0 reading
3. **Check trust_score** — scores < 50 suggest the skill needs verification before relying on it
4. **Prefer repo_skill=true** — maintained alongside the library
5. **Check updated_at** — stale skills may describe outdated APIs
