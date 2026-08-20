# Decisions

Long-lived project decisions. Do not reverse these casually.

If a new Agent believes a decision must change, record a new decision that supersedes the old one. Do not silently overwrite history.

---

## D001 — Single-file distributed product

Date: 2026-08-20

Status: Accepted

Decision:

The core product used by ordinary users is:

`skill-shelf-check.html`

Reason:

The target user should not need Node, npm, a CLI, a server, or an install flow.

---

## D002 — Repository can contain development materials

Date: 2026-08-20

Status: Accepted

Decision:

Although the end-user product is one HTML file, the GitHub repository can and should keep:

- fixtures
- docs
- acceptance record
- open-source maintenance files

Reason:

The project must be re-checkable, maintainable, and contributable.

---

## D003 — Read-only diagnostics

Date: 2026-08-20

Status: Accepted

Decision:

This tool only finds and displays problems that need review.

It must not:

- delete
- modify
- auto-clean
- auto-fix

user Skills.

Reason:

The product is a visual checkup, not a cleaner. Automatic mutation is out of scope and unsafe for non-developer users.

---

## D004 — Local-first privacy

Date: 2026-08-20

Status: Accepted

Decision:

Files actively chosen by the user are processed only locally in the current browser page.

The project must not add:

- analytics
- telemetry
- file upload
- external scripts
- implicit network requests

Reason:

The original requirement is a fully offline, no-upload tool. Hidden network activity would break that promise.

---

## D005 — No unnecessary build stack

Date: 2026-08-20

Status: Accepted

Decision:

This project does not use:

- React
- Vue
- Svelte
- npm
- Vite
- Webpack
- other build dependencies

unless a future product-specification change is made by an explicit human decision.

An Agent must not change this on its own.

Reason:

The distributed product is a single HTML file. A build stack would add install cost the target user does not have, and would violate the project’s technical boundary.

---

## D006 — Directory selection uses webkitdirectory

Date: 2026-08-20

Status: Accepted

Decision:

The page selects a local folder with:

`<input type="file" webkitdirectory multiple>`

The File System Access API (`showDirectoryPicker`) must not be the only selection path.

Reason:

Ordinary users double-click a local HTML file. That use must not require a server, an extra permission model, or a secure-context API as the sole entry.

---

## D007 — Minimal front matter parsing

Date: 2026-08-20

Status: Accepted

Decision:

SKILL.md parsing uses a minimal, dependency-free front matter reader.

It only extracts top-level `name` and `description` from a `---` / `---` block at the start of the file.

It does not implement full YAML. It does not add a YAML library.

The original `SKILL.md` text is stored unchanged as `rawText` for later exact full-text comparison.

Reason:

The product only needs two scalar fields. A full YAML implementation would add complexity and, if done as a dependency, would violate the single-file, no-install boundary.

---

## D008 — Diagnostic Semantics

Date: 2026-08-20

Status: Accepted

Decision:

STEP 5 implemented exactly these five diagnostics, with the definitions below. A later Agent must not invent a different algorithm, tokenizer, threshold, or suppression rule.

These rules are implemented and development-verified in STEP 5. This Decision remains the Source of Truth for diagnostic meaning. Do not change the algorithm in later STEPs.

### Missing required fields

Required fields:

- `name`
- `description`

A field is missing if any of the following is true:

- the key does not exist
- the parser did not produce that field
- the value is `null`
- the value is an empty string after leading and trailing whitespace are checked

A key that exists with an empty value stays `present` in the parser model. Diagnostics still treat that empty content as missing.

Do not auto-fill. Do not guess `name` from the directory path.

### Same name

Report same name only when two or more Skills have identical, valid, non-empty `name` strings from the parser.

Use the actual parsed string. Do not fuzzy-match, compute similarity, use aliases, or ask an AI.

Empty names are not compared here; they are Missing required fields.

One same-name group lists every involved Skill path. Do not emit a separate pair for every direction. Skills A, B, and C with the same name are one group containing A, B, and C.

### Exact full-text duplicate

Compare the STEP 4 `rawText` strings with strict equality:

`skillA.rawText === skillB.rawText`

Do not trim, lowercase, normalize Unicode, rewrite line breaks, strip BOM, strip front matter, or remove whitespace before comparing.

The product definition is “the full SKILL.md text is identical,” not “similar after normalization.”

Three or more Skills with the same `rawText` form one duplicate group listing every involved path.

### Similar description

Definition: Jaccard similarity over normalized word-token sets.

Eligible pair: both Skills have a valid, non-empty description. Missing or empty descriptions are not compared.

Compute on a comparison copy. Do not mutate the stored original description.

Normalization, in this order:

1. Unicode `NFKC`
2. lowercase
3. word-level segmentation with `Intl.Segmenter("und", { granularity: "word" })`
4. keep only segments where `isWordLike === true`
5. drop empty tokens
6. unique tokens as a Set

Do not: remove stop words, stem, lemmatize, expand synonyms, translate, embed, call a remote service, or use AI semantic similarity.

Formula, for token sets A and B:

`similarity = |A ∩ B| / |A ∪ B|`

If either token set is empty, do not emit a similar-description issue.

Report only when `similarity >= 0.80`. Keep the full number internally. UI may later show a percentage such as `84%`. Thresholding must not use a rounded display value.

If `Intl.Segmenter` is missing, do not silently switch to another tokenizer that would change scores. Mark similar-description as unavailable / unsupported for that scan. Still run the other four diagnostics. Do not fail the whole scan. Do not add a third-party tokenizer.

### Redundancy suppression

If a group of Skills is an Exact full-text duplicate, do not also emit Similar description for pairs inside that same duplicate group, even when Jaccard would be 1.0.

Exact duplicate is the stronger, more specific signal.

Same name is not suppressed. `fixtures/conflict/` `duplicate-a` and `duplicate-b` may report Same name and Exact full-text duplicate together, and must not also report Similar description.

If two Skills have different `rawText` but identical descriptions, report Similar description with similarity 1.

### Broken local Markdown link

MVP meaning: an ordinary inline Markdown link in `SKILL.md` whose destination is a local relative file path, and the resolved target is not in the File set the user chose for this scan.

Examples:

- `[Reference](references/format.md)` is checked
- `[Missing](references/missing.md)` is reported if that file is absent from the chosen set

Scope: only inline links in the Skill’s own `SKILL.md`. Do not recurse into support Markdown files.

Do not report as broken local files:

- `http://...`
- `https://...`
- `mailto:...`
- other explicit URI schemes
- `//example.com/...`
- `#section`
- destinations that start with `/`

Do not fetch or otherwise verify external URLs on the network.

Resolve relative destinations from the directory that contains the current `SKILL.md`. Support `references/a.md`, `./references/a.md`, and `../shared/a.md`. Resolve `.` and `..` segments.

Existence is only against files the user provided through the directory picker. Do not read the rest of the filesystem.

For existence checks, drop `#fragment` and `?query`. `references/a.md#usage` checks `references/a.md`.

Percent-encoded destinations may be decoded safely. If decoding throws, do not fail the scan; fail that destination safely.

This MVP does not claim full CommonMark. Reference-style links, heavily escaped destinations, and nested-parenthesis cases are unsupported unless later extended. Do not pretend to support them.

### Independence

The five checks are independent except for one suppression:

Exact full-text duplicate suppresses Similar description for the same duplicate pair or group.

A Skill may receive every other applicable check. A Skill with a broken link and a missing description should get both results.

### Paths

Every diagnostic result must include the involved scan-root-relative paths. Do not show only `name`. Do not store or emit absolute disk paths.

Same-name and duplicate groups list every Skill path in the group.

A broken-link result must keep at least:

- source Skill path
- original link destination
- resolved relative target path

### Determinism

Given the same input files and a browser that supports the required APIs, results come only from these rules.

Do not use an LLM, randomness, the network, a hidden heuristic, or the current time to decide diagnostics.

Reason:

Without a locked definition, a later Agent would invent a tokenizer, a threshold interpretation, or extra suppression, and fixture results would drift. This Decision is the Source of Truth for diagnostic meaning. Product-level wording remains in `docs/PRODUCT_SPEC.md`; implement STEP 5 from this Decision.

---

## D009 — Product UI mapping and JSON export

Date: 2026-08-20

Status: Accepted

Decision:

STEP 6 consumes STEP 5 diagnostic results. It must not change diagnostic meaning.

A Skill is **需复核** when its scan-root-relative path appears in at least one diagnostic issue. For `broken-local-link` and `missing-field`, the source Skill path is the involved path. For `same-name`, `exact-duplicate`, and `similar-description`, every path in `issue.paths` is involved.

`reviewSkillCount` is the number of unique Skills involved in any issue. `normalSkillCount` is the rest. Always:

`normalSkillCount + reviewSkillCount = skillCount`

Issue count in the summary and in JSON is `diagnostics.issues.length`, not the number of affected Skills.

JSON export is a dedicated serializable projection. Filename: `skill-shelf-check-report.json`. It must not include `rawText`, `body`, `File` / `skillFile`, other file bodies, absolute disk paths, browser history, or environment data.

Empty scans (`skillCount = 0`) must not export a report.

`source.type` is `local-directory` after a real folder selection, and `built-in-demo` after the in-page example. Demo export uses `rootName: null`. The built-in example is in-memory data embedded in the HTML. It must not fetch fixtures or use the network.

Reason:

Later Agents would otherwise stringify the live scan object, leak Skill bodies, or treat “需复核” as a new diagnostic.

---

## D010 — MIT License

Date: 2026-08-20

Status: Accepted

Decision:

This repository is licensed under the MIT License.

The copyright line is:

`Copyright (c) 2026 Skill Shelf Check contributors`

Reason:

The project is a lightweight, dependency-free, single-file local tool. MIT allows broad use, modification, and redistribution, while the copyright and license notice are retained.
