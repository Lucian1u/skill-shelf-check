# Implementation Plan

This is the project roadmap.

Execute only the current STEP in `docs/PROJECT_STATUS.md`.
Do not implement later STEPs early.

---

## STEP 1 — Project Foundation & Persistent Context

Establish:

- Project folder
- Required directory structure
- Long-lived product specification
- Agent handoff rules
- Roadmap
- Status record
- Architecture / product decision log
- Minimal HTML scaffold
- Acceptance record file
- Scaffold self-check

Do not implement product features in this STEP.

---

## STEP 2 — Build Real Fixture Sets

Create real test data for:

- `fixtures/normal/`
- `fixtures/conflict/`
- `fixtures/empty/`

These fixtures must match the cases in `docs/PRODUCT_SPEC.md`.

Do not implement scanning or UI in this STEP.

---

## STEP 3 — Local Directory Selection & File Discovery

Implement:

- HTML opened as a local file
- User-initiated directory selection
- Discovery of files named exactly `SKILL.md`
- Grouping by Skill directory
- Basic file information: relative path, file count, total bytes, last modified time

---

## STEP 4 — Skill Parsing

Implement:

- Minimal front-matter parsing
- `name`
- `description`
- Body kept as-is
- Basic structured Skill model

Do not introduce an external YAML library.

---

## STEP 5 — Diagnostics

Implement the five checks from the product specification:

- Missing fields
- Duplicate names
- Exact full-text duplicates
- Similar descriptions (token similarity 0.80)
- Broken local Markdown relative links

Each result must list involved relative paths.

---

## STEP 6 — User Interface & Export

Implement:

- First-screen usage instructions
- Skill cards
- Problem list
- Filters: 全部 / 需复核 / 正常
- Search
- JSON export
- Built-in example
- Privacy statement
- Collapsible 「边界」 section

---

## STEP 7 — Acceptance & Bug Fixing

Really run:

- `fixtures/normal/`
- `fixtures/conflict/`
- `fixtures/empty/`

Record actual results in `acceptance.md`.

Do not pre-write `pass`.

---

## STEP 8 — Open Source Repository Polish

Only after the product behaves for real, complete as needed:

- README
- LICENSE
- CONTRIBUTING
- CODE_OF_CONDUCT
- SECURITY
- Issue templates
- PR template
- CHANGELOG

---

## STEP 9 — Git & GitHub Publishing

Only then:

- Initialize Git
- Inspect commit contents
- Create an independent GitHub repository
- First commit
- Publish the remote repository

---

## STEP 10 — v1.0.0 Release

Complete:

- Release notes
- Tag
- `v1.0.0`
- Final single-HTML user product
- Download / usage notes
- Final re-verification
