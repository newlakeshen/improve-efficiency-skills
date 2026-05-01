---
name: claude-code-review
description: Use when the user asks to run Claude Code CLI as an external code reviewer, asks for "claude code review", wants Claude to inspect local changes, or wants a second-opinion review through the claude command-line tool.
---

# Claude Code Review Skill Guide

## Running a Review
1. Confirm the CLI is available before using it:
   ```bash
   command -v claude && claude --version
   ```
2. Treat Claude Code as an external reviewer, not an authority. Codex remains responsible for judging whether findings are real before presenting or applying them.
3. Default to a read-only review unless the user explicitly asks Claude Code to edit files.
4. Run Claude Code from the repository root so it sees the same git context:
   ```bash
   claude -p --permission-mode dontAsk --output-format text "Review the current git changes. Focus on correctness bugs, regressions, security issues, data loss risks, and missing tests. Do not modify files. Return only actionable findings with file/line references when possible."
   ```
5. Capture stdout/stderr and summarize the outcome for the user in Chinese unless the user requested another language.
6. Before relaying findings, verify them against the local code or diff. Drop findings that are speculative, duplicate, or contradicted by the code.

## Prompt Patterns

### Review Current Changes
```bash
claude -p --permission-mode dontAsk --output-format text "Review the current git changes against HEAD. Focus on correctness, edge cases, tests, and maintainability. Do not modify files. Return prioritized findings with file/line references."
```

### Review a Specific Commit or Branch
```bash
claude -p --permission-mode dontAsk --output-format text "Review the diff for <commit-or-branch>. Focus on behavioral regressions and missing tests. Do not modify files. Return actionable findings only."
```

### Ask for a Second Opinion on a Finding
```bash
claude -p --permission-mode dontAsk --output-format text "Act as a peer reviewer. Evaluate whether this finding is valid in the current repo: <finding>. Inspect the relevant code and explain whether it is a real issue. Do not modify files."
```

### Let Claude Code Propose Fixes Without Editing
```bash
claude -p --permission-mode dontAsk --output-format text "For each confirmed issue in the current diff, propose the smallest safe fix. Do not modify files."
```

## Editing Mode
Only use editing mode when the user explicitly asks Claude Code to implement fixes or make changes.

```bash
claude -p --permission-mode acceptEdits --output-format text "Fix the confirmed review findings with minimal edits. Keep scope limited to the findings. Run or describe relevant validation."
```

After an editing-mode run:
- Inspect `git status --short`.
- Review every changed file before continuing.
- Do not assume Claude's edits are correct; run the relevant local tests or checks.

## Following Up
- Continue the most recent Claude Code session with:
  ```bash
  claude -c -p "Follow up on the previous review: <prompt>"
  ```
- Resume a specific session with:
  ```bash
  claude -r <session_id> -p "<prompt>"
  ```
- After a Claude Code command finishes, tell the user they can continue the same review with `claude -c` if needed.

## Output Handling
- For code review responses, lead with confirmed findings ordered by severity.
- Include file and line references when available.
- Separate "Claude suggested" from "confirmed in local code" when a finding was not independently verified.
- If Claude reports no issues, say that clearly and mention any validation gaps.
- If Claude exits non-zero, report the command failure and do not fabricate review results.

## Prerequisites
- `claude` CLI installed and available on `PATH`.
- Claude Code authenticated and able to run non-interactively.
- For repository reviews, run from a trusted local checkout.
