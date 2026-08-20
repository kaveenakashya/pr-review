---
description: Quick review — coding standards and test coverage only. Faster than full-review; skips security and breaking-change checks.
argument-hint: [staged | <PR#> | <file paths>]
---

The user wants a quick standards-and-coverage review. Target: **$ARGUMENTS**

Resolve the diff target:
- empty → `git -C C:/PestPac.NET diff master...HEAD`
- `staged` → `git -C C:/PestPac.NET diff --cached`
- a number → `gh pr diff $ARGUMENTS --repo WorkWave/PestPac`
- anything else → treat as file path(s) — `git -C C:/PestPac.NET diff -- $ARGUMENTS`

Dispatch `standards-checker` and `test-coverage-checker` **in parallel** (a single message with two Agent calls). Present their combined findings in one structured inline report:

```
## Quick Review

### Standards
<findings from standards-checker>

### Test Coverage
<findings from test-coverage-checker>
```
