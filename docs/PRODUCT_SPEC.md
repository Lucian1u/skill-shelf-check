# Product Specification — Skill Shelf Check

This file is the long-lived product specification and the Source of Truth for product requirements.

This repository is self-contained. A new Agent must recover all product requirements from this file and the other documents in this repository. Files outside this repository are not required to run or continue the project.

Historical provenance: the first draft of these requirements was distilled from an original task sheet named `A-20260820-01_Skill书架体检页.md`. That file is source material / provenance only. It is not a runtime dependency. If it is missing, continue using this specification.

Do not treat old chat history, Agent guesses, summaries, or non-explicit context as a replacement for this file.

If the current user explicitly asks to change product requirements, technical boundaries, or settled decisions, follow `AGENTS.md`: write the change into the applicable repository files first, then implement from the updated spec. Do not refuse a clear, intentional product change by citing this file.

Classification used below:

- **Product Requirement** — what the product must do for the user
- **Technical Constraint** — how it must be built
- **Acceptance Requirement** — how it will be proven
- **Out of Scope** — what it must not do

Nothing below is implemented unless `docs/PROJECT_STATUS.md` and the source say so.

---

## 1. Product Identity

| Field | Value |
|---|---|
| Formal name | Skill Shelf Check |
| Chinese working name | Skill 书架体检页 |
| Artifact type | Single-file offline webpage |
| Distributed user product | `skill-shelf-check.html` |
| One-sentence definition | 选择一个本地 Skills 文件夹，网页只读扫描其中所有 `SKILL.md`，用卡片和冲突清单告诉用户哪些值得复核。 |

### Product Requirement

Skill Shelf Check is:

- Zero-install
- Fully offline
- Local-only
- Read-only
- A single HTML file

The user double-clicks the HTML and actively chooses a local Skills root directory. The page scans `SKILL.md` files and helps the user find Skills that need review.

---

## 2. Problem

### Product Requirement

After non-developers install many Agent Skills, they cannot easily see:

- Duplicate names
- Full-text duplicates
- Near-identical descriptions
- Broken local references

Skills are spread across subdirectories. File names alone do not show whether two Skills share a name, share the same `SKILL.md` body, have almost the same description, or point at a local file that does not exist.

What users lose first is the ability to see what they installed and which items conflict.

### Evidence preserved from research (not rewritten)

These are source facts already deposited in this specification. They justify why the product exists. They are not product features. They do not require any file outside this repository.

- [Simon Smith](https://x.com/_simonsmith/status/2029713209179988445) raised de-duplication, organization, discovery, and update problems as a Skill library grows.
- [Tessl](https://x.com/tessl_io/status/2016938081555804272) pointed out that copying static Skill files across repositories creates stale, out-of-sync copies.
- [Anthropic Skills Issue #919](https://github.com/anthropics/skills/issues/919) reproduced 17 Skills being loaded twice, wasting context and creating routing ambiguity.
- [Reddit 2026-08-19](https://www.reddit.com/r/SideProject/comments/1uy3gea/skill_over_80_how_can_this_be_resolved/) described installing 80+ Skills, still adding more, and actually using only a few.
- Community timing signal: discussion has moved from “how to install a Skill” to “how to maintain too many Skills.” The original posts must not be rewritten as this project’s main post.

### Existing tools checked

| Existing tool | What it already does | What it still lacks for this user | Decision in the original research |
|---|---|---|---|
| [skill-check](https://github.com/thedaviddias/skill-check) | Structure, description, link, and duplicate checks; can export HTML | Requires install or running a command; ordinary users cannot double-click and choose a folder | Worth doing, but only as a zero-install visual entry. Do not clone its full rule set. |
| [skill-cleaner](https://github.com/steipete/agent-scripts/blob/main/skills/skill-cleaner/SKILL.md) | Analyzes duplicates, budget, source, and recent use | Depends on Node, logs, and specific directory knowledge; heavier than a first-look checkup | Worth doing, but do not read logs and do not auto-suggest deletion. |

This product is not trying to be the most complete linter. Its only intended difference is: zero-install, read-only, local, visual. After problems are found, users can be pointed toward specialist tools.

---

## 3. Target User

### Product Requirement

Primary users:

- Designers, creators, and AI users who keep collecting Skills from GitHub, communities, or plugin markets
- People who will install Skills but do not want to configure Node or a CLI first

Account / demo fit from the original task:

- The audience only needs to understand: choose a folder → cards appear → conflicts are visible
- They do not need to understand Agent architecture, APIs, or the command line
- The product should be operable by a non-developer and recordable as a screen demo

---

## 4. Why This Product Form

### Product Requirement

A single offline HTML file is the correct form because:

- Double-clicking the HTML is enough to start
- Directory selection and file reading use native browser capabilities
- No server, account, or install step is required

Browser capability cited by the original research (implementation constraint, not a current feature):

- [MDN File API](https://developer.mozilla.org/en-US/docs/Web/API/File_API/Using_files_from_web_applications)
- [`webkitdirectory`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLInputElement/webkitdirectory)

These APIs allow a page to read file content, metadata, and relative paths after the user actively chooses a local directory.

---

## 5. Input

### Product Requirement

Input is one directory actively chosen by the user, including files in its subdirectories.

Directory entry (implemented in STEP 3):

- Use `<input type="file" webkitdirectory multiple>`
- Read files only after the user chooses a directory

Skill discovery rules:

- Only a file whose name is exactly `SKILL.md` is a Skill entry
- Supporting files in the same directory as that `SKILL.md` are grouped with it
- Display relative path, file count, total bytes, and last modified time

A second entry exists for demonstration:

- “Load built-in example”
- Demonstrates 3 Skill cards and 2 kinds of problems
- Does not access local files

---

## 6. Output

### Product Requirement

The page must show:

- Total Skill count
- For each Skill: name, description, path, file count, last modified time
- Lists for:
  - Duplicate names
  - Full-text duplicates
  - Near-identical descriptions
  - Missing fields
  - Broken local Markdown links
- JSON export

JSON rules:

- JSON contains only the structured results of the current scan
- JSON must not include the body of other files that were not used for checks

---

## 7. Five Diagnostic Checks

### Product Requirement

The product implements exactly these five result types. Each result must list the relative paths involved.

| ID | Check | Rule |
|---|---|---|
| 1 | Missing fields | `name` or `description` is missing, including a present key whose value is empty after trim |
| 2 | Duplicate name | Two or more Skills share the same non-empty parsed `name` |
| 3 | Full-text duplicate | Two or more `SKILL.md` `rawText` strings are strictly identical |
| 4 | Similar description | Jaccard similarity of normalized word-token sets is **>= 0.80** |
| 5 | Broken local Markdown link | An ordinary inline Markdown link in `SKILL.md` points at a local relative file that is not in the chosen scan set |

Exact diagnostic semantics — empty vs missing, Jaccard / `Intl.Segmenter`, the 0.80 threshold, duplicate-group suppression of similar description, link scope, and unsupported-Segmenter behavior — are locked in `docs/DECISIONS.md` **D008**. STEP 5 must follow D008. Do not invent a different algorithm.

Parsing rules for those checks:

- Use a minimal front-matter parser
- Read only top-level `name` and `description`
- Keep the body as-is for full-text equality comparison
- Do not introduce an external YAML library

Grouping rules:

- Conflicting Skills still display as separate directories
- Do not merge them
- Do not delete them

---

## 8. User Interface Requirements

### Product Requirement

When the UI is implemented (not in STEP 1):

- Card view of Skills
- Conflict / review list
- Filters: 全部 / 需复核 / 正常
- Keyword search
- JSON export
- Built-in example button
- Usage instructions on the first screen
- A collapsible “边界” (boundaries) section
- Privacy boundary text shown clearly on the page

Usage instructions live in the page itself. Do not create a separate end-user manual file.

Required privacy sentence on the page:

> 文件仅在当前浏览器标签页内处理；本页没有网络请求，也不会修改所选目录。

The page must not add:

- Analytics
- External scripts
- An automatic-fix button

---

## 9. Privacy Boundary

### Product Requirement

- Files chosen by the user are processed only in the current browser tab
- The page has no network request
- The page does not modify the chosen directory

### Technical Constraint

The project must not add:

- Analytics
- Telemetry
- File upload
- External scripts
- Implicit network requests

User files stay local to the current browser page.

---

## 10. Technical Constraints

### Technical Constraint

- Main file: `skill-shelf-check.html`
- CSS and JavaScript are fully embedded
- No CDN
- No framework
- No remote fonts
- No server
- No Node / npm
- No build system
- No account
- No install step
- Official first-party capability for later directory reading: File API and `webkitdirectory`

Fixtures are for making and accepting the product. They are not packed into the final distributed HTML.

---

## 11. Out of Scope

### Out of Scope

The minimum version must not:

- Use the network
- Upload files
- Modify or delete files
- Read browsing history
- Read Agent logs
- Judge whether Skill content is factually outdated
- Perform security scanning
- Promise syntax compatibility across all Agents
- Auto-clean or auto-fix Skills
- Suggest deletion automatically
- Clone the full rule set of skill-check
- Depend on Node, logs, or a specific directory layout the way skill-cleaner does
- Require CLI knowledge from the end user

---

## 12. Repository File Roles

These files belong to the project. Only the HTML is the end-user product.

| Path | Role |
|---|---|
| `skill-shelf-check.html` | Distributed user product. CSS and JS embedded. |
| `fixtures/normal/` | Example input for making and acceptance. Not shipped inside the HTML. |
| `fixtures/conflict/` | Example input for making and acceptance. Not shipped inside the HTML. |
| `fixtures/empty/` | Example input for making and acceptance. Not shipped inside the HTML. |
| `acceptance.md` | Acceptance record: browser, open method, actual input, result, console status. |
| Page first screen + “边界” section | End-user instructions. No separate user manual file. |
| Built-in example button | In-page demo of 3 Skill cards and 2 problem types. Does not access local files. |

---

## 13. Fixtures

### Acceptance Requirement

Three fixture sets will exist. Formal fixture content is created in STEP 2, not earlier.

### `fixtures/normal/`

- 2 Skills
- Names and descriptions are different
- Required fields are complete
- All local links exist

Expected later result:

- 2 Skill cards
- 0 “需复核” problems
- Filter and JSON export work

### `fixtures/conflict/`

- 3 Skills
- 2 have the same name and identical `SKILL.md` full text
- The other 1 references a missing `references/missing.md`

Expected later result:

- Report duplicate name, full-text duplicate, and 1 broken link at the same time
- All three directories still display separately
- Do not merge
- Do not delete

### `fixtures/empty/`

- No `SKILL.md`
- Only ordinary text and subdirectories

Expected later result:

- Show “没有找到 SKILL.md”
- Do not generate an empty report
- Do not allow export
- Prompt the user to choose a directory again

---

## 14. Acceptance Cases

### Acceptance Requirement

| Case | Input | Expected output | Current result |
|---|---|---|---|
| 正常 | `fixtures/normal/` | 2 Skill cards; 0 review problems; filter and JSON export work | PASS — see `acceptance.md` |
| 边界 | `fixtures/conflict/` | Duplicate name, full-text duplicate, and 1 broken link reported together; three directories still shown separately; no merge; no delete | PASS — see `acceptance.md` |
| 失败/拒绝 | `fixtures/empty/` | Show “没有找到 SKILL.md”; no empty report; export not allowed; prompt to choose again | PASS — see `acceptance.md` |

Acceptance records must include:

- Browser
- How the file was opened
- Actual input
- Actual result
- Console status

All three cases must pass before product status can become `ready` and acceptance status can become `pass`.

Do not write `pass` before a real run.

---

## 15. Completion Standard

The product is complete only when all of the following are true:

1. `skill-shelf-check.html` exists as a single offline file with embedded CSS and JS
2. The three fixtures exist and match the cases above
3. `acceptance.md` records real browser runs of all three cases
4. Those three cases have actually passed

Until then:

- Product status is not `ready`
- Acceptance status is not `pass`

---

## 16. Later Publishing Direction

These items are not current work. They are preserved so later STEPs do not invent a different story.

### Screen recording (after acceptance)

- First frame: conflict list and Skill total on the same screen
- Problem reproduction: open an example directory that contains duplicates and a bad reference
- Trigger: double-click the HTML, click “选择 Skills 文件夹”
- Input / output: choose `fixtures/conflict/`, show duplicate name, full-text duplicate, and broken link
- Failure path: choose `fixtures/empty/`, show a clear rejection and a way to choose again
- Distribution: only one HTML; copy and double-click. Shot list and voiceover wait until acceptance passes.

### Possible posts (after acceptance)

1. Required main post: show how a directory with many Skills becomes readable locally, with real input, findings, usage, and the “do not auto-delete” boundary.
2. Optional second post: only if acceptance reveals an independent increment, explain why the page stays read-only instead of auto-cleaning Skills. It must not repeat post 1.

---

## 17. Source Trace

This section is historical provenance only. It is not a runtime dependency.

The original task sheet `A-20260820-01_Skill书架体检页.md` was the first source material. It is not part of this repository and is not required to continue development. This file is the deposited, complete product-requirements Source of Truth. If the original task sheet does not exist, continue from this repository alone.

| Item | Value |
|---|---|
| Original task id | A-20260820-01 |
| Original date | 2026-08-20 |
| Original research status | pass |
| Original product status | proposed |
| Original acceptance status | pending |
| Original demo status | pending |
