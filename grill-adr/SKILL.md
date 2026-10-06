---
name: grill-adr
description: Interview user with grilling method, write result as ADR/implementation-plan doc matching project's adr/ docs. Use when user invokes /grill-adr or asks to produce ADR (or plan/spec doc) by grilling.
---

Run grilling interview, write outcome as ADR matching project's existing `adr/` docs.

## 1. Resolve destination

Determine path before interviewing. If user didn't specify:

1. Find ADR dir (default `adr/`; also check `docs/adr/`, `doc/adr/`, `decisions/`). List files.
2. Derive next `NNNN-*.md` sequence number.
3. Propose concrete path (e.g. `adr/0003-foo.md`), **ask user to confirm or override** via question tool.

Don't skip even if you can guess. Respect user-supplied path verbatim.

## 2. Grill

Load **grilling** skill, follow exactly: one question at a time, wait each answer, look up facts yourself, offer recommended answer, don't write doc until user confirms shared understanding. Keep running list of resolved decisions — they become decisions table.

## 3. Write document

Mirror structure, heading style, prose voice, detail of existing `adr/` files. Established format:

- `# <Title>` — short context paragraph.
- `## Goals` — what change must achieve.
- `## Resolved Decisions` — numbered `| # | Decision |` table of every decision, in order. Core artifact.
- Design sections as needed (`## Project Structure`, `## Dependencies`, `## Data Model`, `## Configuration`, `## UI`), fenced code blocks for trees, config, types.
- `## Implementation` — split into **PRs** (below).
- `## Risks & Prerequisites`, `## Verification`.

### Split Implementation into PRs

- Minimum one PR per repo needing changes.
- Break repo's work into multiple PRs when large enough, by coherent, independently reviewable slices.
- Order PRs, note dependencies (e.g. "PR 2 depends on 1").
- Within each PR, group work into **phases** of **numbered steps**.

Use `### PR 1 — <repo>: <summary>` headings, then phase subheadings and numbered steps. If existing docs use different format (e.g. classic Context/Decision/Consequences), match those.

Write file, report path. Document is deliverable — implement nothing, stop for approval.
