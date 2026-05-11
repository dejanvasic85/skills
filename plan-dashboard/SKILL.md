---
name: plan-dashboard
description: 'Scan planning documents, parse YAML frontmatter, and regenerate docs/planning/*/_index.md dashboard files with current pipeline status summaries.'
argument-hint: 'No arguments. Example: /plan-dashboard'
user-invocable: true
---

# Skill: plan-dashboard

## Trigger

Use this skill when the user says:

- "update dashboard"
- "show pipeline status"
- "what's the status"
- "planning status"
- invokes `/plan-dashboard`

## Purpose

Scan all planning documents, parse their YAML frontmatter, and regenerate the two `_index.md` dashboard files with current status summaries.

## Output

Updates both dashboard files:

- `docs/planning/ideas/_index.md`
- `docs/planning/plans/_index.md`

## Workflow

### Step 1 — Scan all planning documents

Read every `.md` file (excluding `_index.md` itself) in:

- `docs/planning/ideas/`
- `docs/planning/plans/` (read `plan.md` inside each subdirectory)

For each file, extract the YAML frontmatter fields.

### Step 2 — Calculate metrics

**For ideas:**

- Count by status: `captured | in-progress | completed | deferred`
- Count by domain
- Identify ideas with `status: captured` that have no `plan:` value (not yet planned)

**For plans:**

- Count by status: `planning | ready | in-progress | blocked | completed`
- Identify blocked plans
- Calculate overall completion percentage from task checkboxes in the plan

### Step 3 — Generate dashboard files

#### `docs/planning/ideas/_index.md`

```markdown
---
updated: 'YYYY-MM-DD'
---

# Ideas

## Summary

| Status      | Count |
| ----------- | ----- |
| Captured    | N     |
| In Progress | N     |
| Completed   | N     |
| Deferred    | N     |
| **Total**   | **N** |

## Ideas

| File                      | Title | Status      | Domain | Captured | Plan      |
| ------------------------- | ----- | ----------- | ------ | -------- | --------- |
| [filename](./filename.md) | title | status      | domain | date     | link or — |

(Sorted by: captured date desc)

## Action Required

- [List any captured ideas with no plan link — needs /plan-from-idea]
```

#### `docs/planning/plans/_index.md`

```markdown
---
updated: 'YYYY-MM-DD'
---

# Execution Plans

## Summary

| Status      | Count |
| ----------- | ----- |
| Planning    | N     |
| Ready       | N     |
| In Progress | N     |
| Blocked     | N     |
| Completed   | N     |
| **Total**   | **N** |

## Plans

| #                         | Title | Status      | Idea      | Started   | Completed |
| ------------------------- | ----- | ----------- | --------- | --------- | --------- |
| [NNN](./NNN-slug/plan.md) | title | status      | idea link | date or — | date or — |

(Sorted by: number asc)

## Pipeline Flow

Ideas → Plans
[N ideas total] → [N plans total]

## Blockers

- [List any plans with status: blocked and note what's blocking them]
```

### Step 4 — Confirm

Report which dashboard files were updated and the high-level counts for each stage.

## Notes

- Parse YAML frontmatter between `---` delimiters at the top of each file
- Checkbox completion: count `- [x]` as done, `- [ ]` as pending
- If a stage directory is empty, write a minimal dashboard noting no items yet
- Preserve the `updated:` frontmatter field with today's date on each write
- Do not modify any non-`_index.md` files in this skill
