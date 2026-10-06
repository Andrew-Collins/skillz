---
name: plan-adr
description: Materialize Implementation section of ADR into per-PR working directories, each with git worktree and tasks.md checklist. Use when user invokes /plan-adr or asks to scaffold PR worktrees/plans from ADR.
---

Turn ADR's `## Implementation` section into ready-to-work PR workspaces: one folder per PR, each with git worktree and `tasks.md`.

## 1. Select ADR

Use ADR user named (path or number); otherwise list ADR directory, **ask which to use** (recommend most recent). Read it, parse `## Implementation` into ordered PRs of form `### PR <n> — <repo>: <summary>`, each with phases and numbered steps. Record ADR's absolute path — embedded in every `tasks.md`.

If no `## Implementation` in that PR format, stop, tell user; don't invent one.

## 2. Resolve output directory

Use user-supplied directory verbatim; otherwise **ask**, recommending path **outside** repo (worktree nested in its own repo is messy), e.g. `<repo-parent>/<repo>-plans/<adr-slug>`.

## 3. Resolve each repo

Collect unique `<repo>` names. For each, find local path (current repo if matches; otherwise search and/or ask), verify git repo, note base branch and `origin` URL.

If one PR spans multiple repos, prefer splitting; if combined, use primary repo for folder name, note others in `tasks.md`.

## 4. Create structure

For each PR in order:

1. Create `<outdir>/<number>_<repo>/`.
2. `git -C <repo-path> worktree add <outdir>/<number>_<repo>/code -b <branch>`
   - `code/` is worktree; branch off repo's base branch, new branch named `<number>_<repo>`.
   - If branch or folder exists, tell user rather than clobber.
3. Write `tasks.md` from template.

### tasks.md template

```markdown
# <number>_<repo> — PR <number>: <summary>

## Summary

<The PR summary from the ADR.>

## How to complete

- **ADR:** `<absolute path to the ADR doc>`
- **Repo:** `<repo>` (<origin URL>)
- **Worktree:** `<absolute path to code/>`
- **Branch:** `<branch>`
- **Base:** `<base branch>`
- **Depends on:** <PR n, or "none">
- **Coordinates with:** <other PRs/repos touched, or "n/a">

## Phases & steps

- [ ] **Phase 1: <name>**
  - [ ] <step>
  - [ ] <step>
- [ ] **Phase 2: <name>**
  - [ ] <step>
```

Phases/steps nested checkbox list (check phase once all steps done). Preserve ADR's phase/step text plus any detail needed to act on it.

## 5. Report

List every created folder, worktree, branch, and PR dependencies. Commit nothing, implement nothing; scaffolds are deliverable, stop for approval.
