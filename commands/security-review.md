---
description: Security-only PR review — checks for XSS, injection, CSRF, and secrets across ASP, C#, React, and SQL changes.
argument-hint: [staged | <PR#> | <file paths>]
---

The user wants a security-focused review. Target: **$ARGUMENTS**

Resolve the diff target:
- empty → `git -C C:/PestPac.NET diff master...HEAD`
- `staged` → `git -C C:/PestPac.NET diff --cached`
- a number → `gh pr diff $ARGUMENTS --repo WorkWave/PestPac`
- anything else → treat as file path(s) — `git -C C:/PestPac.NET diff -- $ARGUMENTS`

Dispatch the `security-scanner` agent with the resolved diff and present its findings verbatim.
