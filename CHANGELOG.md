# Changelog

All notable changes to this project will be documented here.

## Unreleased

## 1.0.0 - 2026-08-20

### Added

- Initial project scaffold.
- Persistent project context structure.
- Local directory selection.
- SKILL.md discovery.
- Skill directory grouping.
- Basic file metadata: file count, total bytes, and latest file modification time.
- Local SKILL.md content reading.
- Minimal name / description front matter parsing.
- Raw markdown preservation.
- Five diagnostic categories.
- Deterministic issue grouping.
- Local Markdown link checking.
- Description similarity.
- Product UI: first-screen usage, Skill cards, and the review list.
- Filters: 全部 / 需复核 / 正常.
- Keyword search across name, description, and path.
- Built-in demo that does not read local files.
- JSON export of the current scan projection.
- Privacy sentence and collapsible 边界 section.
- Full project README for GitHub visitors.
- MIT license.
- Contribution guidelines.
- Contributor Covenant Code of Conduct 2.1.
- Security policy.
- GitHub issue forms.
- Pull request template.

### Fixed

- Directory file input is wrapped in a `hidden` host so the accessibility tree only exposes the visible “选择 Skills 文件夹” button.
- Results page is issues-first: filters, search, and JSON export live in the Skills section so search no longer sits far above the cards it controls.
