---
name: plan-from-idea
description: 'Generate a detailed execution plan directly from an idea file. Searches the codebase first, asks only what it cannot infer, then writes the plan. Use when the user says "create plan", "plan this idea", "plan the work", or invokes /plan-from-idea.'
argument-hint: 'Idea filename or slug (optional). Example: 2026-05-add-llms-txt or docs/planning/ideas/2026-05-add-llms-txt.md'
user-invocable: true
---

# Skill: plan-from-idea

## Trigger

Use this skill when the user says:

- "create plan"
- "plan this idea"
- "plan the work"
- "create execution plan"
- invokes `/plan-from-idea`

## Purpose

Generate a detailed execution plan directly from an idea file. This is stage 2 of the planning pipeline — there is no separate PRD step.

## Output

- Creates: `docs/planning/plans/NNN-slug/plan.md`
- Creates: `docs/planning/plans/NNN-slug/assets/.gitkeep`
- Updates: idea file's `plan:` and `status:` frontmatter fields

## Workflow

### Step 1 — Identify the idea

If the user referenced a specific idea file or slug, read it. If not, list ideas with `status: captured` from `docs/planning/ideas/` and ask which one to plan.

Read the idea file fully before proceeding.

### Step 2 — Cross-reference the codebase

Before asking any questions or writing anything, search the codebase thoroughly:

- Search for files, components, schemas, routes, and types mentioned or implied by the idea
- Identify actual file paths that need changing (use Glob/Grep to verify they exist)
- Look for related tests, queries, and data mappers that may be affected
- Check `docs/planning/plans/` to see the numbering of existing plans

This step is mandatory — the plan must reference real file paths, not guesses.

### Step 3 — Ask only what you cannot infer

After the codebase search, assess what is genuinely unclear. Ask **only** for information that:

- Cannot be reasonably inferred from the idea file
- Cannot be answered by reading the codebase
- Would materially change the scope or approach of the plan

If the idea is clear and the codebase confirms the scope, **skip this step entirely** and proceed to Step 4.

If questions are needed, ask them all at once in a single message — maximum 3 questions. Do not run a multi-round interview. Do not ask for things you can look up.

Examples of good questions:
- "Should this replace the existing X or live alongside it?"
- "Is there a specific endpoint format required for Y?"

Examples of bad questions (never ask these):
- "What problem does this solve?" — it's in the idea file
- "What's the desired outcome?" — it's in the idea file
- "What priority should this carry?" — not tracked

### Step 4 — Determine the plan number

Scan `docs/planning/plans/` for existing plan directories. The next number is the highest existing number + 1 (zero-padded to 3 digits, e.g., `010`). Use the true next available number, skipping no gaps.

### Step 5 — Generate the plan

**Directory and filename:**

- Derive slug from the idea title in kebab-case
- E.g., idea "Add llms.txt file" → `docs/planning/plans/010-add-llms-txt/plan.md`

**Frontmatter:**

```yaml
---
title: '[Idea title]: Execution Plan'
number: '[plan number]'
status: planning
created: 'YYYY-MM-DD'
updated: 'YYYY-MM-DD'
idea: '[idea filename]'
started: ''
completed: ''
estimated-hours: ''
tags: [from idea]
---
```

**Body structure** (follow `docs/planning/templates/plan.md` if it exists):

- **Overview** — one paragraph: what this plan delivers and what idea it implements
- **Phases** — break work into logical phases ordered by dependency:
  - Schema/CMS changes before frontend
  - Backend/API before UI
  - Foundation before content
- **Each task** must include:
  - Specific file paths (verified against codebase in Step 2)
  - Numbered steps describing exactly what to change
  - Verification statement
- **Sanity CMS Steps** — if the idea involves CMS schema or content, list them separately
- **Verification Checklist** — always include in this order:
  1. `npm run format`
  2. `npm run lint`
  3. `npm run type:check`
  4. `npm run build`
  5. `npm run test:e2e`
- **Rollback Plan** — how to undo if something breaks in production

### Step 6 — Create assets directory

Create `docs/planning/plans/NNN-slug/assets/.gitkeep` to ensure the directory is tracked.

### Step 7 — Update the idea file

Patch the idea file's frontmatter:

- Set `plan:` to the plan path (e.g., `010-add-llms-txt/plan.md`)
- Set `status: in-progress`

### Step 8 — Confirm

Report the files created/updated. Ask if the user wants to start working on the plan immediately.

## Task ordering rules

For SEO/content ideas:

1. Schema/CMS changes first
2. TypeScript types and validation schemas
3. Data queries
4. Data mappers
5. Components/pages
6. Content creation
7. E2E tests

For engineering ideas:

1. Schema/model changes
2. Service layer
3. API routes
4. UI components
5. Tests

## Notes

- Plans directory: `docs/planning/plans/`
- Template: `docs/planning/templates/plan.md`
- Always verify file paths exist in the codebase before referencing them
- Search the project structure to discover the correct path prefixes — do not assume a specific project layout
- There is no PRD step — go directly from idea to plan
