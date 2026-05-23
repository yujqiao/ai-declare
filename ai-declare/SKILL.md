---
name: ai-declare
description: Use when the user invokes "/declare" to add or update an AI-DECLARATION.md file and badge in their project. Handles AI usage transparency declaration per the DimwitLabs spec.
---

# AI Declaration

Add an `AI-DECLARATION.md` file and README badge to the current project following the [DimwitLabs AI-DECLARATION.md](https://ai-declaration.md) specification.

## When to Use

Triggered by the `/ai-declare` command:
- `/ai-declare` — asks user for level, then creates/updates declaration
- `/ai-declare <level>` — uses the specified level directly

This skill should also be used when the user asks to "declare AI usage", "add an AI declaration", or "set up AI-DECLARATION".

## Levels

| Level | Meaning |
|-------|---------|
| `none` | Human acts alone; no AI involvement |
| `hint` | AI surfaces suggestions passively |
| `assist` | Human prompts; AI acts on a part of the task |
| `pair` | Both act equally; Human understands internals |
| `copilot` | AI acts on the whole task, prompting Human for permission |
| `auto` | AI acts autonomously to completion |

## Process

### Step 1: Determine the level

If the user provided a level with the command (e.g. `/declare copilot`), validate it against the six valid levels. If valid, proceed. If invalid, tell the user and list valid options.

If **no level was specified**, ask the user to select one:

> Which AI involvement level best describes this project?
> 1. **none** — No AI tools used
> 2. **hint** — AI surfaced suggestions passively
> 3. **assist** — AI helped with parts of tasks
> 4. **pair** — Human and AI collaborated equally
> 5. **copilot** — AI generated most code with human oversight
> 6. **auto** — AI built the project autonomously

Wait for the user to respond before proceeding.

### Step 2: Determine the scope

Ask the user if they want to declare **processes** (granular per-phase) or just the global level:

> Would you like to declare per-process levels (design, implementation, testing, etc.) or just a single global level?
> 1. **Global only** — One level for everything
> 2. **Per-process** — Different levels for different phases

If they choose per-process, ask about each process:
- `design` — Architecture, system design, decision-making
- `implementation` — Writing production code
- `testing` — Writing tests, test plans, QA
- `documentation` — Writing docs, comments, READMEs
- `review` — Code review, PR feedback
- `deployment` — CI/CD config, infra, release scripts

### Step 3: Create or update AI-DECLARATION.md

Write `AI-DECLARATION.md` in the project root. Use version `0.1.1`.

**Minimal template (global level only):**

```markdown
---
version: "0.1.1"
level: <level>
---

This format is based on [AI-DECLARATION.md](https://ai-declaration.md/en/0.1.2).

## Notes

- <leave blank for user to fill>
```

**With processes:**

```markdown
---
version: "0.1.1"
level: <highest-level>
processes:
  design: <level>
  implementation: <level>
---

This format is based on [AI-DECLARATION.md](https://ai-declaration.md/en/0.1.2).

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

- **Wrong version string**: Must be `"0.1.1"` (quoted, with patch version)
- **Global level lower than process levels**: The global `level` must be the maximum of all declared process levels
- **Forgetting the `## Notes` section**: It's required by the spec even if empty
- **Invalid level names**: Only the six defined levels are valid
- **Overwriting user notes**: When updating an existing file, preserve any `## Notes` content the user has written

## Reference

- Spec homepage: https://ai-declaration.md
- Spec repo: https://github.com/DimwitLabs/AI-DECLARATION.md
- Current version: 0.1.1
