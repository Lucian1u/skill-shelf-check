# Acceptance Test Record

## Candidate

- Product file: `skill-shelf-check.html`
- Date: 2026-08-20
- Git commit: none (repository is not a Git repo yet)

## Environment

- OS: macOS
- Automated browser: Playwright Chromium (`Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/151.0.0.0 Safari/537.36`)
- Open method for automated runs: local static HTTP (`http://127.0.0.1`) because Playwright MCP blocks `file://`
- Fixture input method: visible button `选择 Skills 文件夹` → hidden `webkitdirectory` input → real fixture directories (`fixtures/normal/`, `fixtures/conflict/`, `fixtures/empty/`)
- `file://` + macOS native directory picker in this STEP: **completed by the user** on the final candidate after the information-architecture fix
- User confirmation (verbatim): “这版没问题。”
- User browser version: not recorded

## Case 1 — Normal

- Input: `fixtures/normal/`
- Expected: 2 Skill cards; 0 review problems; filters and JSON export work; both local Markdown references exist
- Actual:
  - Skill count: 2
  - Cards: `image-organizer` (path `image-organizer`, fileCount 2, reference `references/naming.md` present) and `summarize-notes` (path `summarize-notes`, fileCount 2, reference `references/format.md` present)
  - Names and descriptions match the fixture `SKILL.md` files
  - Issues: 0
  - Review: 0
  - Normal: 2
  - Filters: 全部 2 / 需复核 0 / 正常 2
  - Issue region: “没有发现需要复核的问题。这只表示当前五类检查没有信号，不是内容保证。”
  - JSON download `skill-shelf-check-report.json`: `schemaVersion` 1, `source.type` `local-directory`, `rootName` `normal`, skillCount 2, issueCount 0, reviewSkillCount 0, normalSkillCount 2, `issues` `[]`, `similarDescriptionAvailable` true; no `rawText`, `body`, `File`/`skillFile`, or `/Users/` paths
- Result: **PASS** (automated)

## Case 2 — Conflict

- Input: `fixtures/conflict/`
- Expected: 3 Skills still shown separately; same-name, exact-duplicate, and 1 broken local link together; no merge/delete
- Actual:
  - Skill count: 3 (`duplicate-a`, `duplicate-b`, `broken-link` as separate cards)
  - Issues: 3, types exactly `same-name`, `exact-duplicate`, `broken-local-link`
  - `same-name` group: name `note-cleaner`; paths `duplicate-a`, `duplicate-b`
  - `exact-duplicate` group: paths `duplicate-a`, `duplicate-b`; page and JSON do not show `rawText`
  - `broken-local-link`: source `broken-link`, destination `references/missing.md`, resolvedTarget `broken-link/references/missing.md`
  - No `missing-field`; similar-description suppressed (count 0)
  - Review: 3; Normal: 0
  - Filters: 全部 3 / 需复核 3 / 正常 0
  - Cards: duplicate-a/b show 需复核 + 同名 + 全文重复; broken-link shows 需复核 + 失效本地链接
  - JSON: skillCount 3, reviewSkillCount 3, normalSkillCount 0, issueCount 3; destination and resolvedTarget retained; no `rawText`/`body`/`skillFile`/`/Users/`
- Result: **PASS** (automated)

## Case 3 — Empty

- Input: `fixtures/empty/`
- Expected: `没有找到 SKILL.md`; no empty report; export not allowed; can choose again
- Actual:
  - Skill count: 0
  - Page text: `没有找到 SKILL.md`
  - No Skill cards, no summary, toolbar hidden, export disabled, `buildExportReport` returns `null`
  - `README.txt` contains the words `SKILL.md` and was not treated as a Skill
  - `选择 Skills 文件夹` and `加载内置示例` remain available
- Result: **PASS** (automated)

## Additional Product Checks

- Demo: PASS — 3 Skills, 2 issue types (同名, 失效本地链接), banner `内置示例`, JSON `source.type` `built-in-demo` / `rootName` null, no fixture fetch
- Search: PASS — name (`summarize-notes`), description (`photo files`), path (`image-organizer` / `duplicate-a`), case-insensitive (`SUMMARIZE-NOTES`, `NOTE-CLEANER`), filter∩search, zero-result `没有匹配的 Skill`, clear restores the current filter list
- Filters: PASS — counts from the current scan; 需复核 on normal shows `没有匹配的 Skill`; new source resets filter to 全部 and search to empty
- JSON: PASS — real download of `skill-shelf-check-report.json` for the normal scan; projection matches schema; empty scan cannot export
- Reselection: PASS — normal → conflict → empty → normal produced 2 → 3 → 0 → 2 Skill counts with no leftover cards
- Privacy / network: PASS for product code — no `fetch(`, XHR, WebSocket, sendBeacon, external script, or stylesheet. Playwright recorded only the test-server GET of the HTML file, plus browser `favicon.ico` 404s (not product-initiated)
- Offline (code): PASS — single HTML, CSS/JS embedded, no CDN/fonts/remote JS
- DOM injection: PASS — synthetic Skill name/description `<img src=x onerror=alert(1)><script>alert(1)</script>` rendered as text only; no `img`/`script` nodes; `alert` did not run
- Accessibility smoke: PASS — visible buttons have names; search labeled `搜索`; after wrapping the file input in `[hidden]`, the accessibility snapshot no longer shows an unnamed button; filters are native radios; `details` expands; 正常/需复核 is text, not color-only; headings h1/h2/h3; 200% zoom had no horizontal overflow. Not a WCAG certification
- Responsive: PASS — ~1280px and ~390px; 390px `scrollWidth - clientWidth = 0`; cards did not overlap; paths wrap; controls remain usable
- Console: PASS — 0 JavaScript errors, 0 unexpected warnings. Favicon 404 is a browser/test-server request
- Self-check: PASS — `runSelfCheck().pass === true` (discovery, parser, diagnostics, UI)

## Information architecture follow-up

Human review after automated acceptance:

- Search logic itself worked (name / description / path).
- The original results page put filters, search, and JSON export above the issue list, so search sat far from the Skill cards it controls. That made it easy to think a search had no results.

Fix in this candidate:

- Results order is now: overview → 需复核问题 → Skills (status filter + search Skill + match count + cards) → 导出完整 JSON 报告 → 边界
- Search and filters only change Skill cards. The issue list and overview numbers stay the full scan.
- JSON export still writes the complete scan, even if cards are filtered.
- Empty scans still show only `没有找到 SKILL.md` (no empty Skills chrome).

The user re-confirmed this layout in the final `file://` smoke test.

## Manual Verification

Agent automated acceptance: **PASS**

- Formal fixture directory scans through the page file input
- Demo, search, filters, JSON download, reselection, privacy/source audit, XSS rendering, a11y snapshot, responsive, console, self-check
- Post-fix conflict checks: search `note-cleaner` (2 matching cards, 3 issues remain); search `broken-link` (1 card, JSON still 3/3); filter 正常 (0 cards, issues remain)

User manual `file://` smoke test: **PASS**

The user opened the final candidate by double-clicking `skill-shelf-check.html` in Finder and confirmed: “这版没问题。”

Recorded from that confirmation, without extra invented detail:

- The final candidate opens via Finder double-click
- The conflict results page hierarchy matches the intended order
- Issues are shown first
- Skill search/filters sit in the same region as the cards
- A `note-cleaner` search is clearly visible
- JSON export works
- The built-in example works
- No obvious errors were reported

## Final Result

**PASS**

- Agent automated acceptance: **PASS**
- User manual file:// smoke test: **PASS**
