# skills

A collection of [Claude Code](https://claude.ai/code) skills for planning and engineering workflows.

## Installation

```sh
npx skills add dejanvasic85/skills
```

This will present an interactive menu to select which skills to install.

## Skills

### Planning pipeline

A three-stage workflow for going from raw idea to executable plan.

| Skill | Slash command | Description |
|---|---|---|
| `capture-idea` | `/capture-idea` | Capture a raw idea into `docs/planning/ideas/` with YAML frontmatter |
| `promote-to-prd` | `/promote-to-prd` | Run an interview to promote an idea into a formal PRD |
| `plan-execution` | `/plan-execution` | Generate a phased execution plan for an approved PRD |
| `planning-dashboard` | `/planning-dashboard` | Regenerate the `_index.md` status dashboards across all planning stages |

**Typical flow:**

```
/capture-idea Add dark mode toggle
    ↓
/promote-to-prd
    ↓
/plan-execution
    ↓
/planning-dashboard
```

### Engineering

| Skill | Slash command | Description |
|---|---|---|
| `fix-renovate-pr` | `/fix-renovate-pr` | Diagnose and fix failing Renovate dependency-upgrade PRs |
