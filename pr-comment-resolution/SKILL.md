---
name: pr-comment-resolution
description: 'Address review feedback on an existing GitHub pull request. Use when Codex needs to inspect PR comments, decide which suggestions to implement or push back on, make and verify scoped fixes, reply in the relevant threads, and resolve addressed review threads.'
argument-hint: 'GitHub PR URL or number. Example: https://github.com/owner/repo/pull/123'
user-invocable: true
---

# PR Comment Resolution

Work from the live PR and current branch. Treat each review thread as a decision record, not a checklist.

## Workflow

1. Fetch the PR metadata, review summaries, issue comments, inline comments, and review-thread resolution state. Use GitHub CLI/API tools when available.
2. Classify each unresolved thread:
   - **Implement**: a concrete bug, missing test, or low-risk improvement that matches the PR scope.
   - **Push back**: a suggestion that is incorrect, redundant, expands scope, conflicts with a contract, or lacks enough value.
   - **Clarify**: a genuine product or design decision that needs user direction.
3. Read the relevant local instructions and inspect the actual code path before deciding. Do not accept a comment purely because it sounds reasonable.
4. Present a concise recommendation when user direction is needed. Otherwise, implement the approved changes tightly; do not refactor unrelated code to satisfy a small comment.
5. Add the smallest behaviour-level regression where a comment exposes a gap. Prefer an existing test file and test the boundary that proves the contract, not just schema acceptance or a mocked layer.
6. Follow repository formatting, build, type-check, lint, and focused-test requirements. Report existing warnings separately from new failures.
7. Commit and push before marking an implementation comment done, unless the user explicitly asks not to publish changes.
8. Reply in each relevant thread:
   - For completed work, use `Done.` if the user requests terse acknowledgement; otherwise say what changed and where.
   - For pushback, give a short evidence-based reason and state the retained behaviour.
   - Do not claim a fix is done until it is pushed and verified.
9. Resolve only the threads the user authorizes. After resolving, re-query thread state and confirm that no intended unresolved threads remain.

## Decision Rules

- Preserve intentional distinctions such as omitted versus explicit-clear values, client validation versus persistence behaviour, and optional draft fields versus submitted fields.
- Do not turn a reviewer request into a broad architectural rewrite unless the evidence requires it.
- Prefer a direct reply over a code change when the current implementation already expresses the intended contract.
- If a review summary requests testing but has no inline thread, leave a general PR comment acknowledging the completed test work when the user asks for every comment to be answered.

## GitHub Operations

Use the GitHub API/CLI to retrieve both inline comments and review-thread state; a PR can be approved while still having unresolved threads.

For mutations:

- Reply to an inline comment using the comment's `in_reply_to` identifier.
- Resolve review threads by their GraphQL thread identifiers, not their REST comment IDs.
- Re-query after mutations. Treat a successful request as insufficient until the visible thread state confirms it.

## Handoff

State the pushed commit, implemented changes, pushbacks, verification commands/results, and remaining threads. Link changed files when useful.
