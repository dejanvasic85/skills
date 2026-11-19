---
name: resolve-sentry-issue
description: 'Use when a Sentry issue is new, regressed or escalating, when a webhook or schedule wakes an agent for one, or when asked to work through open Sentry issues. Covers real bugs, expected errors that are only noise, bots and unsupported browsers, and issues that come back after a fix.'
argument-hint: 'Sentry issue short ID or URL. Example: WEB-8. With none, works through unresolved issues one at a time.'
user-invocable: true
---

# Resolve a Sentry issue

## Outcome

Each run leaves the issue in a state that will not wake an agent again for the same cause:

- a pull request that fixes it, with `Fixes <ISSUE-ID>` in the commit, or
- a note on the issue and a question to the human, when this skill says to stop.

Archiving or ignoring an issue is never the outcome. An archived issue returns when it escalates, and every return is another wake-up.

## Inputs

- A Sentry issue short ID or URL, or a wake-up payload that names one.
- With none: list unresolved, unassigned issues (`is:unresolved is:unassigned`) by event count and take them one at a time. One issue, one pull request. When step 1 says stop for an issue, skip it and take the next one.

## Steps

### 1. Check whether it is already handled

Search before reading any code:

- open and merged pull requests, and commit messages, that name the issue ID
- notes on the Sentry issue

Then act on what you find:

| Found | Do |
|---|---|
| The issue is assigned to a person, or has a note starting `Waiting on a human` | Stop. A person owns it now. |
| An open pull request names the issue | Stop. The fix is waiting for review. |
| A merged fix that no release has shipped yet | Stop. Sentry resolves the issue when the release ships. |
| A merged fix, and every new event comes from a release older than the fix release | Resolve the issue in the fix release, add a note, stop. |
| A merged fix, and new events come from the fix release or later | Go to [Stop rules](#stop-rules). The fix did not work. |
| Nothing | Go on. |

### 2. Find the cause

Read the latest event, its stack trace and its tags (browser, release, URL, user count). Read the code at the culprit frame and its callers. Check the tag spread: one browser, one URL or one region for every event is a strong sign of a bot or an unsupported client.

### 3. Classify it, then act

| Class | Signs | Action |
|---|---|---|
| Real bug | Wrong behaviour that a person could hit | Fix the root cause in a pull request, using the project's task workflow. |
| Expected behaviour | The code throws on purpose and the app already recovers (a refusal, a redirect, a "not found") | Filter it out of Sentry in code, in the SDK's `beforeSend`, in a pull request. Leave the app's behaviour alone. |
| Unsupported client | Every event comes from a browser older than the build supports, or from a bot | Stop that client from reporting, for example by not starting the browser SDK when a feature the app needs is missing, in a pull request. |
| Outside the code | A provider outage, a network blip, infrastructure | Go to [Stop rules](#stop-rules). |

A filter or a skipped client is a fix in code, reviewed like any other. Write a test for it and document what is no longer reported where the project documents monitoring.

### 4. Open the pull request

- Put `Fixes <ISSUE-ID>` in the commit message, so Sentry resolves the issue when the release ships.
- In the description, give the evidence: the event count, the tag spread, the cause and the class.
- Run the project's checks and code review. Never merge. The human reviews and merges.

### 5. Close the loop

Add one note to the Sentry issue: the class, the cause in one line, and the pull request link.

## Stop rules

Stop, assign the issue to the human, and add a note that starts `Waiting on a human:` with the question. Later runs skip an assigned issue, so it is not triaged again. Do this when:

- the issue came back on a release that has its fix. A second fix on the same guess is how loops start, so the cause needs a person.
- the cause is outside the code.
- the fix needs a product decision, or would change behaviour people rely on.

Never, in any case:

- merge or deploy
- archive or ignore an issue as the fix
- change alert rules or Sentry project settings

## Red flags

| Thought | Reality |
|---|---|
| "It is only a warning, so I will leave it open." | Open means the next event wakes an agent again. Fix it, filter it, or stop and ask. |
| "Filters hide errors, so I will not touch them." | A filter in code, reviewed in a pull request, is the fix for expected behaviour. |
| "The app should return a value instead of throwing." | If the app already recovers, its behaviour is right. The report is the problem. |
| "The last fix did not hold, so I will try another." | Stop and ask. The cause is not what it seemed. |
| "Archive it until it escalates." | It will escalate, and you will be here again. |

## Note template

```
Class: <real bug | expected behaviour | unsupported client | outside the code>
Cause: <one line>
Evidence: <events, releases, tag spread>
Action: <pull request link, or the question for the human>
```
