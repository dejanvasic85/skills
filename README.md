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

### Engineering

| Skill | Slash command | Description |
|---|---|---|
| `fix-renovate-pr` | `/fix-renovate-pr` | Diagnose and fix failing Renovate dependency-upgrade PRs |
