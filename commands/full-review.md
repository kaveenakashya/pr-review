---
description: Four-agent parallel PR review — standards, security, test coverage, and breaking changes. Posts to GitHub after confirmation.
argument-hint: [staged | <PR#> | <file paths>]
---

The user wants a full parallel PR review. Target: **$ARGUMENTS**

Invoke the `full-review` skill. Interpret the target:
- empty → changes on this branch vs master (`git diff master...HEAD`)
- `staged` → only staged changes (`git diff --cached`)
- a number (e.g. `12471`) → that PR via `gh pr diff 12471 --repo WorkWave/PestPac`
- anything else → treat as file path(s)

Pass the resolved diff and target type to the skill.
