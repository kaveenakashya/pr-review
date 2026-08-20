---
name: full-review
description: Orchestrates a four-agent parallel PR review — standards, security, test coverage, and breaking changes — then synthesizes one report and offers to post it to GitHub.
---

You review a pull request across four dimensions in parallel and produce one structured report.

## Step 1 — Resolve the diff

Determine what to review based on the target passed in:
- **empty or branch** → run `git -C C:/PestPac.NET diff master...HEAD --name-only` to list files, then `git -C C:/PestPac.NET diff master...HEAD` for the full diff
- **`staged`** → `git -C C:/PestPac.NET diff --cached`
- **a number (PR#)** → `gh pr diff <number> --repo WorkWave/PestPac` and `gh pr view <number> --repo WorkWave/PestPac --json title,number,headRefName,body`
- **file paths** → `git -C C:/PestPac.NET diff -- <paths>`

If `gh` is not available (`command -v gh` fails on Mac/Linux or `where gh` on Windows), fall back to `git -C C:/PestPac.NET diff master...HEAD` for PR numbers and note it under Gaps.

Classify changed files by extension:
- `.asp`, `.inc` → ASP/VBScript
- `.cs` → C# / .NET
- `.js`, `.jsx` → React/JavaScript
- `.sql` → SQL
- files with `test`, `spec`, `Test`, `Spec` in the name, or under `tests/`, `test/`, `__tests__/` → Test files (count separately, not reviewed)

## Step 2 — Fan out (parallel)

Dispatch all four agents concurrently (a **single message with four Agent calls**), passing:
- The full diff text
- The file classification summary (which file types are present)
- The PR number and title (if available)

Agents to dispatch:
- `standards-checker`
- `security-scanner`
- `test-coverage-checker`
- `breaking-change-detector`

Each returns a `## Findings` + `## Confidence` block. "None found" and "not applicable" are valid results — do not retry.

## Step 3 — Synthesize

Produce one structured inline review:

```
## PR Review — #<number>: <title>
*(or: Branch review — <branch name> | Staged changes review)*

### 🔴 Security (<N> issues)
<findings from security-scanner, or "✅ None found">

### 🟡 Standards (<N> issues)
<findings from standards-checker, or "✅ None found">

### 🟡 Test Coverage (<N> gaps)
<findings from test-coverage-checker, or "✅ All changed files have coverage">

### 🔍 Breaking Changes
<findings from breaking-change-detector, or "✅ None detected">

---
**Confidence:** Security: <level> | Standards: <level> | Coverage: <level> | Breaking: <level>

**Gaps:** <anything that could not be checked — e.g. gh not installed, no local DB>
```

## Step 4 — Offer to post (PR number only)

If the target was a PR number and `gh` is available, ask:
```
Post this review to PR #<number>? (yes/no)
```

If confirmed with "yes", post:
```bash
gh pr review <number> --repo WorkWave/PestPac --comment --body "<the full synthesized review>"
```

Do NOT post if the target was a branch diff, staged changes, or file paths — only when reviewing a specific PR by number.

## Rules

- Read-only everywhere. Never edit, stage, or commit files.
- Post to GitHub only after explicit "yes" from the user.
- If `gh` is unavailable, skip PR fetch and post; note under Gaps.
- Do not invent findings. Report "None found" as clean.
- Do not collapse multiple findings into a vague summary — keep each `file:line` entry.
