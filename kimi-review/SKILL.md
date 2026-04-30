---
name: kimi
description: Use when the user asks to run Kimi CLI (kimi exec, kimi resume) or references Moonshot AI Kimi for code analysis, refactoring, automated editing, or review tasks.
---

# Kimi Skill Guide

## Running a Task
1. Use the default model configured in the local Kimi config file (do not ask the user to select a model).
2. Determine the execution mode required for the task; default to applying local edits since yolo mode is enabled by default:
   - **Standard execution (default)**: `--print --yolo`
   - **Read-only review or analysis**: `--print --yolo` (Kimi will still be able to read files and produce output, but will not write changes unless explicitly asked in the prompt)
   - **Full autonomous / unattended mode**: `--print --yolo --afk`
   - **Plan mode (dry run)**: `--plan`
3. Assemble the command with the appropriate options:
   - `--print` (for non-interactive execution)
   - `--yolo` (auto-approve tool calls, enabled by default)
   - `--afk` (away-from-keyboard mode, auto-dismisses questions — use only when truly unattended)
   - `--thinking` (enabled by default)
   - `-w, --work-dir <directory>`
   - `-p, --prompt "your prompt here"` or `-c, --command "your prompt here"`
4. Run the command, capture stdout/stderr, and summarize the outcome for the user.
5. **After Kimi completes**, inform the user: "You can resume this Kimi session at any time by saying 'kimi continue' or asking me to continue with additional analysis or changes."

### Quick Reference
| Use case | Key flags |
|---|---|
| Standard execution (default) | `kimi --print --yolo --thinking -p "prompt"` |
| Read-only review or analysis | `kimi --print --yolo --thinking -p "prompt"` |
| Full autonomous / unattended | `kimi --print --yolo --afk --thinking -p "prompt"` |
| Resume recent session | `kimi --continue -p "new prompt"` or `kimi -C -p "new prompt"` |
| Resume specific session | `kimi --resume <session_id> -p "new prompt"` |
| Run from another directory | `kimi -w <dir> --print --yolo --thinking -p "prompt"` |
| Plan mode (dry run) | `kimi --plan --thinking -p "prompt"` |

## Following Up
- After every `kimi` command, summarize the results and ask if the user wants to continue with `kimi --continue` or start a new task.
- When continuing, use: `kimi --continue -p "new prompt"`. The continued session automatically uses the same model and configuration from the original session.
- When resuming a specific session, use: `kimi --resume <session_id> -p "new prompt"`.
- Restate the execution mode when proposing follow-up actions.

## Critical Evaluation of Kimi Output

Kimi is powered by Moonshot AI models with their own knowledge cutoffs and limitations. Treat Kimi as a **colleague, not an authority**.

### Guidelines
- **Trust your own knowledge** when confident. If Kimi claims something you know is incorrect, push back directly.
- **Research disagreements** using WebSearch or documentation before accepting Kimi's claims. Share findings with Kimi via continue if needed.
- **Remember knowledge cutoffs** - Kimi may not know about recent releases, APIs, or changes that occurred after its training data.
- **Don't defer blindly** - Kimi can be wrong. Evaluate its suggestions critically, especially regarding:
  - Model names and capabilities
  - Recent library versions or API changes
  - Best practices that may have evolved

### When Kimi is Wrong
1. State your disagreement clearly to the user
2. Provide evidence (your own knowledge, web search, docs)
3. Optionally continue the Kimi session to discuss the disagreement. **Identify yourself as the current agent** so Kimi knows it's a peer AI discussion. Use your actual model name:
   ```bash
   kimi --continue -p "This is <your_model_name> following up. I disagree with [X] because [evidence]. What's your take on this?"
   ```
4. Frame disagreements as discussions, not corrections - either AI could be wrong
5. Let the user decide how to proceed if there's genuine ambiguity

## Error Handling
- Stop and report failures whenever `kimi --version` or a `kimi` command exits non-zero; request direction before retrying.
- When output includes warnings or partial results, summarize them and ask how to adjust.

## Prerequisites
- `kimi` CLI installed and available on `PATH`.
- Kimi configured with valid credentials (run `kimi login` if needed).
- Confirm the installation by running `kimi --version`; resolve any errors before using the skill.
