---
name: impl-adr
description: Implement PR's tasks.md phase by phase with review-and-fix loop, human tuicr review, commit per phase. Use when user invokes /impl-adr or when spawn-adr launches plan window to start implementing plan.
---

Implement `tasks.md` one phase at a time. Run from PR plan directory (`tasks.md` beside `code/`, git worktree); do all code work in `code/`.

## Ground rules

- Follow applicable loaded skills and repo conventions.
- Before coding, inspect `code/` for existing patterns, match them (naming, structure, error handling, test style).
- Keep `tasks.md` checkboxes updated as steps complete.

## Per phase

For each unchecked phase in `tasks.md`, in order:

1. **Implement** phase's steps in `code/`.
2. **Review**: run `cr-adr` skill.
3. **Fix high & medium**, re-run `cr-adr`. Repeat until only **low/judgement** and/or **cyclical** findings remain.
   - Cyclical = finding asking to undo/change back something previous review cycle introduced (loop oscillating).
   - Never auto-fix low/judgement or cyclical findings.
4. **Resolve remaining findings with user**: prompt (one at a time) each — Fix, Ignore, or own answer. Action every answer; if one raises new decision or finding, prompt again until settled.
5. **Human review in tuicr**: prompt user to review in tuicr pane, wait. When they say ready, use `tuicr` skill to read and action all comments, prompt review again, wait. Repeat until user says continue.
6. **Commit and continue**: commit in `code/` per repo conventions (fallback: `<plan-folder>: <phase name>`), mark phase done in `tasks.md`, start next phase.

## Finish

When all phases done, report commits made and plan status, stop. Don't push or open PRs unless asked.
