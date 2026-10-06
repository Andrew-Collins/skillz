---
name: spawn-adr
description: Launch tmux window per PR plan directory using flimpl script, each with tuicr review pane (in code/) and opencode pane (in plan dir) that starts implementing tasks.md. Use when user invokes /spawn-adr or asks to start implementing plans produced by plan-adr.
---

Follow-up to `plan-adr`: launch one `flimpl` tmux window per PR plan directory, tuicr against worktree, opencode against plan.

## 1. Resolve plan root

Use user-supplied root; otherwise **ask** (recommend directory `plan-adr` last used, or `plans/...` path). Contains `<number>_<repo>` folders.

List subdirectories matching `<number>_<repo>`, ordered by `<number>`. Each must have `tasks.md` and `code/` worktree; skip and report any missing them.

## 2. Require tmux

`flimpl` needs tmux. If `$TMUX` unset, stop, tell user to run from tmux session (or `tmux new -s <name>` and re-run).

## 3. Launch window per PR

For each `<root>/<number>_<repo>`:

- Window name: `<number>_<repo>` (exactly folder name).
- Window/opencode dir: PR plan dir (`<root>/<number>_<repo>`) so opencode can read `tasks.md`.
- tuicr dir: `<root>/<number>_<repo>/code` so reviews target worktree.
- tuicr cmd: `tuicr-wait` (via `TUICR_CMD`) so the pane waits for the first change instead of exiting on the empty worktree.
- opencode prompt: always `/impl-adr`; never substitute.

```bash
TUICR_DIR="<root>/<number>_<repo>/code" \
TUICR_CMD=tuicr-wait \
  flimpl "<number>_<repo>" "<root>/<number>_<repo>" "/impl-adr"
```

If `flimpl` reports window exists, skip PR, note it.

## 4. Report

List windows created (name, plan dir, tuicr dir) and PRs skipped with reasons. Launching windows is whole job; implement nothing.
