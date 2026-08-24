# skills

A collection of [Claude Code](https://claude.ai/code) skills for planning and engineering workflows.

## Installation

```sh
npx skills add dejanvasic85/skills
```

This will present an interactive menu to select which skills to install.

## Skills

### Planning pipeline

A two-stage workflow for going from raw idea to executable plan.

| Skill | Slash command | Description |
|---|---|---|
| `plan-idea` | `/plan-idea` | Capture a raw idea into `docs/planning/ideas/` with YAML frontmatter |
| `plan-from-idea` | `/plan-from-idea` | Generate a phased execution plan directly from an idea |
| `plan-dashboard` | `/plan-dashboard` | Regenerate the `_index.md` status dashboards for ideas and plans |

**Typical flow:**

```
/plan-idea Add dark mode toggle
    ↓
/plan-from-idea
    ↓
/plan-dashboard
```

### SEO / GEO

| Skill | Slash command | Description |
|---|---|---|
| `seo-scorecard` | `/seo-scorecard` | Write the monthly SEO/GEO scorecard from fresh GSC + GA4 exports, diffed against the prior month and verified against live CMS content rather than plan checkboxes |

### Invoicing

| Skill | Slash command | Description |
|---|---|---|
| `git-invoice` | `/git-invoice` | Scan git logs for a period and produce categorised invoice line items (Maintenance / Feature Work with size estimates) |

### Engineering

| Skill | Slash command | Description |
|---|---|---|
| `fix-renovate-pr` | `/fix-renovate-pr` | Diagnose and fix failing Renovate dependency-upgrade PRs |
| `pr-comment-resolution` | `/pr-comment-resolution` | Address, reply to, and resolve pull-request feedback |
