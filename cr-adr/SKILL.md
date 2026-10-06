---
name: cr-adr
description: "Two-axis code review (Standards + Spec) of change, where Spec axis reviews against PR's tasks.md and originating ADR. Runs both axes as parallel sub-agents, reports side by side with severities. Use when user invokes /cr-adr, when impl-adr asks for phase review, or to review changes against plan."
---

Two-axis review of diff between `HEAD` and fixed point:

- **Standards**: code conform to repo's documented coding standards?
- **Spec**: code faithfully implement `tasks.md` and its ADR?

Both axes run as **parallel sub-agents** so they don't pollute each other's context; this skill aggregates findings. Spec source is PR's tasks/plan, never issue tracker.

## Location

Expect `plan-adr` directory: `tasks.md` beside `code/` git worktree. Tasks `./tasks.md`; repo under review `./code` (all git commands run there). Honor different path if user passed one.

## 1. Pin fixed point

Default to current phase's uncommitted changes: `git -C code diff HEAD`. If user passed fixed point (commit, branch, tag, `main`, `HEAD~5`), use `git -C code diff <fixed-point>...HEAD` (three-dot), note commits via `git -C code log <fixed-point>..HEAD --oneline`. Confirm ref resolves, diff non-empty before spawning sub-agents.

## 2. Spec source

Read `tasks.md`, take **ADR:** path, read ADR too (design context and rationale; `tasks.md` has concrete steps).

Scope to **current phase** — normally `tasks.md`'s first unchecked phase, i.e. one diff implements. If unclear, infer from checkboxes and commit history; ask only if still ambiguous. **Slice `tasks.md` to that phase**: pass only its heading and steps to Spec sub-agent. Later phases intentionally unimplemented, must not be reviewed.

## 3. Standards sources

Repo docs for how code should be written (`CODING_STANDARDS.md`, `CONTRIBUTING.md`, `AGENTS.md`, linter/formatter configs) plus nearby code patterns.

On top, Standards axis always carries **smell baseline** (Fowler _Refactoring_ ch.3). Two rules: documented repo standard overrides baseline; every smell is labelled judgement call, never hard violation. Skip anything tooling enforces. Match each against diff:

- **Mysterious Name**: name doesn't reveal what it does/holds. → rename; if no honest name comes, design's murky.
- **Duplicated Code**: same logic shape in more than one hunk/file. → extract shared shape.
- **Feature Envy**: method reaches into another object's data more than its own. → move onto data it envies.
- **Data Clumps**: same few fields/params keep travelling together. → bundle into one type.
- **Primitive Obsession**: primitive/string standing in for domain concept. → give concept its own type.
- **Repeated Switches**: same `switch`/`if`-cascade on same type recurs. → polymorphism or shared map.
- **Shotgun Surgery**: one logical change forces scattered edits. → gather what changes together.
- **Divergent Change**: one file edited for several unrelated reasons. → split by reason.
- **Speculative Generality**: abstraction/params/hooks for needs spec lacks. → delete; inline until real need shows.
- **Message Chains**: long `a.b().c().d()` navigation. → hide walk behind one method.
- **Middle Man**: class/function that mostly delegates. → cut it, call target direct.
- **Refused Bequest**: subclass ignoring most of what it inherits. → prefer composition.

## 4. Spawn both sub-agents in parallel

**Standards**: include diff command and commit list; standards files from step 3 **plus smell baseline pasted in full**; brief: "Report, per file/hunk, (a) every cited violation of documented standard and (b) any baseline smell, named with hunk quoted. Documented-standard breaches may be hard; baseline smells always judgement calls; repo standards override baseline. Skip anything tooling enforces. Tag each `high`, `medium`, or `low/judgement`. Under 400 words."

**Spec**: include diff command and commit list; **current phase's slice of `tasks.md`**, linked ADR, phase name; brief: "Report against current phase only: (a) steps missing or partial; (b) work not asked for (scope creep); (c) steps implemented wrong or contradicting ADR. Later phases out of scope — never report them as missing. Quote relevant line each finding. Tag each `high`, `medium`, or `low/judgement`. Under 400 words."

## 5. Aggregate

Present two reports under `## Standards` and `## Spec`, lightly cleaned, each finding tagged:

- **high** — hard repo-standard violation, tasks/ADR requirement missing or wrong, or correctness bug.
- **medium** — real issue worth fixing (duplication, feature envy, scope creep) that isn't blocking.
- **low/judgement** — preferences, optional smells, anything repo doesn't mandate.

**Scope filter before presenting.** Drop finding naming another phase / complaining later phase unimplemented, or not tied to changed hunk (Spec finding must point at diff line and current-phase line it maps to). Dropped finding removed, not reworded, downgraded, or listed out of scope.

Do **not** merge or rerank across axes (see _Why two axes_). End with one-line summary: findings per axis by severity, worst issue each axis.

## Why two axes

Change can pass one axis and fail other — standards-perfect code implementing wrong thing, or exact-spec code breaking conventions. Reporting separately stops one masking other.
