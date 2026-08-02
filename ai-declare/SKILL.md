---
name: ai-declare
description: Use when the user invokes "/ai-declare" to add or update an AI-DECLARATION.md file and badge in their project. Handles AI usage transparency declaration per the DimwitLabs spec.
---

# AI Declaration

Add an `AI-DECLARATION.md` file and README badge to the current project following the [DimwitLabs AI-DECLARATION.md](https://ai-declaration.md) specification.

## When to Use

Triggered by the `/ai-declare` command:
- `/ai-declare`: asks user for level, then creates/updates declaration
- `/ai-declare <level>`: uses the specified level directly

This skill should also be used when the user asks to "declare AI usage", "add an AI declaration", or "set up AI-DECLARATION".

## Spec Fidelity

The API is the single source of truth. Present level and process descriptions exactly as returned. Do **not** rephrase or paraphrase them.

## Process

### Step 0: Fetch the spec

The spec evolves, so read it at runtime rather than hardcoding it:

| Request | Gives you |
|---|---|
| `GET https://ai-declaration.md/api/versions` | `current`: the version to write |
| `GET https://ai-declaration.md/api/levels` | Valid levels with verbatim descriptions, ordered by `rank` (least to most AI involvement) |
| `GET https://ai-declaration.md/api/processes` | Valid processes with verbatim descriptions |

**If the API is unreachable:** say so, then use spec `0.1.2` with levels `none`, `hint`, `assist`, `pair`, `copilot`, `auto` (lowest to highest). Ask the user to describe their own involvement rather than inventing spec wording.

### Step 1: Determine the level

If a level was given (e.g. `/ai-declare copilot`), check it against Step 0's levels; if invalid, say so and list the valid ones.

Otherwise ask which level fits, listing each level and its description from Step 0. Wait for an answer.

### Step 2: Determine the scope

Ask whether they want one global level, or per-process levels. If per-process, ask about each process from Step 0, presenting its description.

### Step 3: Write AI-DECLARATION.md

Write to the project root, using `<version>` from Step 0:

```markdown
---
version: "<version>"
level: <level>
---

This format is based on [AI-DECLARATION.md](https://ai-declaration.md/en/<version>).

## Notes

- <leave blank for user to fill>
```

For per-process levels, add a `processes` key to the frontmatter:

```yaml
processes:
  design: <level>
  implementation: <level>
```

The global `level` must be the **highest** level among all declared processes. Any process not listed is implicitly `none`.

If the file already exists, update it rather than overwriting, preserving any user-written `## Notes`.

### Step 4: Add badge to README.md

If `README.md` exists, insert the badge for the global level after the title (`# ...`), or at the top if there is no title. Replace an existing AI-DECLARATION badge rather than adding a second.

| Level | Badge |
|-------|-------|
| `none` | `[![AI-DECLARATION: none](https://img.shields.io/badge/䷼%20AI--DECLARATION-none-dcfce7?labelColor=dcfce7)](https://ai-declaration.md)` |
| `hint` | `[![AI-DECLARATION: hint](https://img.shields.io/badge/䷼%20AI--DECLARATION-hint-ecfccb?labelColor=ecfccb)](https://ai-declaration.md)` |
| `assist` | `[![AI-DECLARATION: assist](https://img.shields.io/badge/䷼%20AI--DECLARATION-assist-fef9c3?labelColor=fef9c3)](https://ai-declaration.md)` |
| `pair` | `[![AI-DECLARATION: pair](https://img.shields.io/badge/䷼%20AI--DECLARATION-pair-ffedd5?labelColor=ffedd5)](https://ai-declaration.md)` |
| `copilot` | `[![AI-DECLARATION: copilot](https://img.shields.io/badge/䷼%20AI--DECLARATION-copilot-fee2e2?labelColor=fee2e2)](https://ai-declaration.md)` |
| `auto` | `[![AI-DECLARATION: auto](https://img.shields.io/badge/䷼%20AI--DECLARATION-auto-ede9fe?labelColor=ede9fe)](https://ai-declaration.md)` |

### Step 5: Validate

Do not assume the file is correct. Check it:

```
POST https://ai-declaration.md/api/validate
Content-Type: text/plain

<the full contents of the file you just wrote>
```

Returns `{ valid, errors, warnings, notes, level, version }`.

- **`errors`**: the file does not conform. Fix and re-validate; do not report success while any remain.
- **`warnings`**: valid but flagged (wrong level ordering, unquoted version, unpublished version). Fix it, or surface it to the user.
- **`notes`**: informational only.

If the API is unreachable, say so and check the file by hand.

### Step 6: Confirm

Summarise: the level written, the badge added/updated, the validation result, and a reminder to fill in `## Notes`.

## Common Mistakes

Step 5 catches anything wrong with the file itself. These two it cannot, because they are about how this skill behaves:

- **Paraphrasing the spec**: level and process descriptions shown to the user must be verbatim from Step 0
- **Overwriting user notes**: when updating an existing file, preserve any `## Notes` the user has written

## Reference

- Spec: https://ai-declaration.md
- API docs: https://ai-declaration.md/api/
- Spec repo: https://github.com/DimwitLabs/AI-DECLARATION.md
- `GET /api/detect?owner=&repo=`: unused here, but reports what a public GitHub repo already declares
