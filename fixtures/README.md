# Fixture test data

These directories are fixed test inputs for Skill Shelf Check.

They are not the end-user product. They are not packed into `skill-shelf-check.html`.

Do not change fixture content or expected answers to make an implementation “pass.”

If a later product-specification change truly requires different fixtures, update the specification and decisions first, then change these files.

---

## `normal/`

A healthy Skills root.

Contents:

- `summarize-notes/SKILL.md` — complete `name` and `description`; body links to `references/format.md`, which exists
- `image-organizer/SKILL.md` — complete `name` and `description`; body links to `references/naming.md`, which exists

Expected later scan result:

- Skill count: 2
- Missing fields: 0
- Duplicate names: 0
- Full-text duplicates: 0
- Similar descriptions: 0
- Broken local Markdown links: 0
- Both Skills are normal (no review needed)

---

## `conflict/`

A Skills root that contains review problems required by the acceptance case.

Contents:

- `duplicate-a/SKILL.md`
- `duplicate-b/SKILL.md`
- `broken-link/SKILL.md`

`duplicate-a/SKILL.md` and `duplicate-b/SKILL.md` are byte-for-byte identical. They share the same `name` (`note-cleaner`) and the same full text.

`broken-link/SKILL.md` has complete fields, a different `name`, different description, different body, and one local Markdown relative link to `references/missing.md`. That target file must not exist.

Expected later scan result (minimum, matching the acceptance case):

- Skill count: 3
- Duplicate name: `duplicate-a` and `duplicate-b`
- Full-text duplicate: `duplicate-a` and `duplicate-b`
- Broken local Markdown relative link: 1, from `broken-link/SKILL.md` to `references/missing.md`
- Three directories stay separate
- Do not merge
- Do not delete

Coverage note (not extra fixtures):

- These three sets do not include a Skill that is only missing `name` or `description`
- They do not include a pair of Skills that are not full-text duplicates but have descriptions near similarity 0.80
- Description similarity for the duplicate pair is suppressed by exact full-text duplicate (D008)
- Tokenization and the similarity metric are locked in `docs/DECISIONS.md` D008: NFKC, lowercase, `Intl.Segmenter`, word-like tokens, Set, Jaccard, `>= 0.80`, and exact-duplicate suppression

---

## `empty/`

A chosen root directory that contains no Skills.

Contents:

- `README.txt`
- `misc/notes.txt`

There is no file named exactly `SKILL.md` anywhere in this tree.

Expected later scan result:

- Skill count: 0
- Page shows `没有找到 SKILL.md`
- Do not generate an empty report
- Do not allow JSON export
- User can choose a directory again

---

## What these fixtures are not

Creating this test data is not product acceptance.

Formal acceptance is STEP 7 and is recorded only in `acceptance.md` after real browser runs.
