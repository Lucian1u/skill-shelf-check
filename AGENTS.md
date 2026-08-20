# AGENTS.md

Rules for any Agent working in this repository.

Repository files are the source of project continuity across conversations.

Old chat history, Agent guesses, summaries, and non-explicit context cannot override repository specifications.

The current user’s explicit, active instruction to change product requirements, technical boundaries, or settled decisions has the highest priority. That change must be written into the repository before it is implemented. See Source of Truth.

---

## Project

- **Name:** Skill Shelf Check
- **Repository directory:** `skill-shelf-check`
- **Core goal:** A zero-install, fully offline, local, read-only, single-file HTML visual diagnostic tool for Agent Skills.
- **End user:** Designers, creators, and AI users who install Skills from GitHub, communities, or plugin markets, and do not want to configure Node or a CLI first.
- **Distributed user product:** One file — `skill-shelf-check.html`.

The user double-clicks the HTML, actively chooses a local Skills root directory, and the page scans `SKILL.md` files to help them find Skills that need review.

---

## Mandatory Reading Order

Any new Agent starting work MUST read, in this order:

1. `AGENTS.md`
2. `docs/PRODUCT_SPEC.md`
3. `docs/PROJECT_STATUS.md`
4. `docs/IMPLEMENTATION_PLAN.md`
5. `docs/DECISIONS.md`
6. Source and test files involved in the current task

Do not recover project truth from old chat history or from files outside this repository. See Source of Truth.

---

## Source of Truth

This repository is self-contained. A new Agent must recover goals, boundaries, decisions, status, and next work from files inside this repository only.

Do not require files outside this repository. An original task sheet may exist as historical provenance. It is not required to run or continue this project.

### Priority

1. **Current user’s explicit, active change request** — highest priority when the user clearly asks to change product requirements, technical boundaries, or existing long-lived decisions.
2. **Repository specifications** — authority for continuity across conversations, and for everything that is not an explicit current-user change.
3. **Old chat history, Agent guesses, summaries, or non-explicit context** — never override repository specifications.

Repository files remain the authority over previous conversations. They do not outrank a clear, current user instruction to change the spec.

Do not refuse a legitimate, explicit product change by citing “PRODUCT_SPEC wins.”

Do not apply an explicit change only in chat. Persist it first.

### Where each concern lives after it is recorded

| Concern | File |
|---|---|
| Product requirements | `docs/PRODUCT_SPEC.md` |
| Current project status | `docs/PROJECT_STATUS.md` |
| Development roadmap | `docs/IMPLEMENTATION_PLAN.md` |
| Settled decisions that must not be casually reversed | `docs/DECISIONS.md` |
| Actual implementation | The source itself |
| Whether something actually passed | `acceptance.md` |

### How to persist an explicit user change

When the current user clearly asks to change product requirements, technical boundaries, or settled decisions:

1. Identify which long-lived files the change affects.
2. Update every applicable file before implementing:
   - `docs/PRODUCT_SPEC.md`
   - `docs/DECISIONS.md`
   - `docs/PROJECT_STATUS.md`
   - `docs/IMPLEMENTATION_PLAN.md` if the roadmap is affected
3. Then implement from the updated specification.

Agents must not silently change product requirements on their own.

### Historical provenance (not a runtime dependency)

The first draft of the product specification was distilled from an original task sheet named `A-20260820-01_Skill书架体检页.md`.

That file is source material / provenance only:

- It is not required to run this project.
- It is not required to continue development.
- New Agents must not depend on it to recover context.
- `docs/PRODUCT_SPEC.md` is the deposited product-requirements Source of Truth.
- If the original task sheet is missing, continue from this repository alone.

---

## Scope Discipline

Agents MUST:

- Execute only the current STEP recorded in `docs/PROJECT_STATUS.md`
- Not implement later STEPs early
- Not expand the product on their own
- Not introduce extra tech stack for “engineering maturity”
- Report contradictions between specification and implementation
- Not silently change product requirements
- Persist an explicit current-user spec change into the applicable repository files before implementing it
- Not refuse an explicit, legitimate product change by citing PRODUCT_SPEC as higher than the current user

Agents MUST NOT:

- Implement directory scanning, `webkitdirectory`, File API, Skill reading, front-matter parsing, diagnostics, search, filters, JSON export, or formal UI before those STEPs
- Install npm, Node, or any dependency
- Introduce any framework, CDN, remote font, or build system
- Create a GitHub remote repository, push, or create a GitHub Release before those STEPs
- Invent features that are not in `docs/PRODUCT_SPEC.md`

---

## Technical Constraints

- The end-user product is a single HTML file
- CSS and JavaScript must be embedded in that HTML in the final product
- No server
- No Node / npm
- No framework
- No CDN
- No remote fonts
- No network access required
- Do not upload user files
- Do not modify the user’s directory
- Do not delete Skills
- Do not auto-fix Skills
- User files are processed only locally in the current browser page

---

## Work Protocol

For every STEP:

1. Read the current status.
2. Confirm the current STEP.
3. Complete only that STEP.
4. Self-check.
5. Run tests when they exist and are required.
6. Update `docs/PROJECT_STATUS.md`.
7. Update `docs/DECISIONS.md` only when a new long-lived decision is made.
8. Update `acceptance.md` if acceptance results are involved.
9. Stop. Do not start the next STEP unasked.

---

## No Fake Completion

Forbidden:

- Claiming tests passed without running them
- Writing “pass” without checking
- Guessing browser behavior
- Inventing acceptance records
- Describing planned features as implemented
- Writing unconfirmed results into README

---

## Language

Project identifiers, repository file names, and the HTML `title` are English.

Product specification content may remain in Chinese when that is the language of the original requirement, so meaning is not rewritten.
