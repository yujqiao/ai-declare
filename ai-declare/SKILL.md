---
name: ai-declare
description: Use when the user invokes "/ai-declare" to add or update an AI-DECLARATION.md file and badge in their project. Handles AI usage transparency declaration per the DimwitLabs spec.
---

# AI Declaration

Add an `AI-DECLARATION.md` file and README badge to the current project following the [DimwitLabs AI-DECLARATION.md](https://ai-declaration.md) specification.

## When to Use

Triggered by the `/ai-declare` command:
- `/ai-declare` — asks user for level, then creates/updates declaration
- `/ai-declare <level>` — uses the specified level directly

This skill should also be used when the user asks to "declare AI usage", "add an AI declaration", or "set up AI-DECLARATION".

## Spec Fidelity

The spec evolves. Do **not** rephrase or paraphrase any text taken from the spec — version strings, level names, and level definitions must be reproduced verbatim. Treat the upstream spec as the single source of truth.

## Process

### Step 0: Fetch the current spec

Before prompting the user, fetch the live spec so that the skill stays in sync with upstream without code changes:

1. Fetch `https://raw.githubusercontent.com/DimwitLabs/AI-DECLARATION.md/main/package.json` and read the `version` field — this is the **current spec version**.
2. Fetch `https://raw.githubusercontent.com/DimwitLabs/AI-DECLARATION.md/main/README.md` and extract the level definitions block (the bullet list under the `Levels` heading, beginning with `` - `none`: ``). These are the **verbatim level definitions** to present to the user and to embed anywhere this skill quotes the spec.

If either fetch fails (offline, rate-limited, etc.), fall back to the frozen snapshot below and tell the user the skill is using the offline fallback.

**Frozen fallback (spec `0.1.2`, 2026-04-14):**

- `none`: Human acts on the task alone with no AI involvement.
- `hint`: Human acts on the task and the AI surfaces suggestions passively.
- `assist`: Human prompts and the AI acts on a part of the task.
- `pair`: Human prompts as both human and AI both act on the task equally; Human understands internals clearly.
- `copilot`: Human prompts and AI acts on the whole task, prompting the Human for permission or clarification.
- `auto`: Human prompts and AI acts autonomously bringing the task to completion.

Use the fetched (or fallback) values for `<version>` and the level definitions in every subsequent step.

### Step 1: Determine the level

If the user provided a level with the command (e.g. `/ai-declare copilot`), validate it against the level names returned in Step 0. If valid, proceed. If invalid, tell the user and list valid options.

If **no level was specified**, ask the user to select one, presenting the level bullets **verbatim** as fetched in Step 0:

> Which AI involvement level best describes this project?
>
> - `none`: Human acts on the task alone with no AI involvement.
> - `hint`: Human acts on the task and the AI surfaces suggestions passively.
> - `assist`: Human prompts and the AI acts on a part of the task.
> - `pair`: Human prompts as both human and AI both act on the task equally; Human understands internals clearly.
> - `copilot`: Human prompts and AI acts on the whole task, prompting the Human for permission or clarification.
> - `auto`: Human prompts and AI acts autonomously bringing the task to completion.

(The bullets above are the frozen fallback; substitute the fetched bullets when available.)

Wait for the user to respond before proceeding.

### Step 2: Determine the scope

Ask the user if they want to declare **processes** (granular per-phase) or just the global level:

> Would you like to declare per-process levels (design, implementation, testing, etc.) or just a single global level?
> 1. **Global only** — One level for everything
> 2. **Per-process** — Different levels for different phases

If they choose per-process, ask about each process:
- `design` — Architecture, system design, and decision-making
- `implementation` — Writing production code
- `testing` — Writing tests, test plans, and quality assurance
- `documentation` — Writing docs, comments, READMEs, and changelogs
- `review` — Code review and pull request feedback
- `deployment` — CI/CD configuration, infrastructure, and release scripts

### Step 3: Create or update AI-DECLARATION.md

Write `AI-DECLARATION.md` in the project root. Use `<version>` from Step 0 (do not hardcode).

**Minimal template (global level only):**

```markdown
---
version: "<version>"
level: <level>
---

This format is based on [AI-DECLARATION.md](https://ai-declaration.md/en/<version>).

## Notes

- <leave blank for user to fill>
```

**With processes:**

```markdown
---
version: "<version>"
level: <highest-level>
processes:
  design: <level>
  implementation: <level>
---

This format is based on [AI-DECLARATION.md](https://ai-declaration.md/en/<version>).

## Notes

- <leave blank for user to fill>
```

The global `level` must be the **highest** level among all declared processes. Any process not listed is implicitly `none`.

If `AI-DECLARATION.md` already exists, update it rather than overwriting. Preserve any user-written `## Notes` content when possible.

### Step 4: Add badge to README.md

Check if `README.md` exists in the project root. If it does, add the appropriate badge near the top (after the title if one exists, otherwise as the first line).

**Badge URLs by level:**

| Level | Badge URL |
|-------|-----------|
| `none` | `[![AI-DECLARATION: none](https://img.shields.io/badge/䷼%20AI--DECLARATION-none-dcfce7?labelColor=dcfce7)](https://ai-declaration.md)` |
| `hint` | `[![AI-DECLARATION: hint](https://img.shields.io/badge/䷼%20AI--DECLARATION-hint-ecfccb?labelColor=ecfccb)](https://ai-declaration.md)` |
| `assist` | `[![AI-DECLARATION: assist](https://img.shields.io/badge/䷼%20AI--DECLARATION-assist-fef9c3?labelColor=fef9c3)](https://ai-declaration.md)` |
| `pair` | `[![AI-DECLARATION: pair](https://img.shields.io/badge/䷼%20AI--DECLARATION-pair-ffedd5?labelColor=ffedd5)](https://ai-declaration.md)` |
| `copilot` | `[![AI-DECLARATION: copilot](https://img.shields.io/badge/䷼%20AI--DECLARATION-copilot-fee2e2?labelColor=fee2e2)](https://ai-declaration.md)` |
| `auto` | `[![AI-DECLARATION: auto](https://img.shields.io/badge/䷼%20AI--DECLARATION-auto-ede9fe?labelColor=ede9fe)](https://ai-declaration.md)` |

Use the global `level` for the badge. If an existing AI-DECLARATION badge is already present, replace it with the updated one. If no badge exists, insert after the title line (the `# ...` heading) or at the top.

### Step 5: Confirm

After creating/updating both files, summarize what was done:

- The `AI-DECLARATION.md` file created/updated with level `<level>`
- The README badge added/updated
- Remind the user to fill in the `## Notes` section with specifics

## Common Mistakes

- **Hardcoding a stale version**: Always use the `<version>` fetched in Step 0; never paste a literal version into the skill
- **Unquoted version string**: The frontmatter value must be a quoted string (e.g. `"0.1.2"`), not a bare number
- **Global level lower than process levels**: The global `level` must be the maximum of all declared process levels
- **Forgetting the `## Notes` section**: It's required by the spec even if empty
- **Invalid level names**: Only the level names returned by Step 0 are valid
- **Paraphrasing the spec**: Level descriptions presented to the user must be verbatim from the upstream README
- **Overwriting user notes**: When updating an existing file, preserve any `## Notes` content the user has written

## Reference

- Spec homepage: https://ai-declaration.md
- Spec repo: https://github.com/DimwitLabs/AI-DECLARATION.md
- Spec version source (fetched at runtime): https://raw.githubusercontent.com/DimwitLabs/AI-DECLARATION.md/main/package.json
- Spec level definitions source (fetched at runtime): https://raw.githubusercontent.com/DimwitLabs/AI-DECLARATION.md/main/README.md
- Frozen fallback version: `0.1.2`
