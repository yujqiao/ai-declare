[![AI-DECLARATION: copilot](https://img.shields.io/badge/䷼%20AI--DECLARATION-copilot-fee2e2?labelColor=fee2e2)](https://ai-declaration.md)

# ai-declare

A skill for coding agents to declare AI usage transparency via the [DimwitLabs AI-DECLARATION.md](https://ai-declaration.md) spec.

## Why

Disclosing your AI usage is a responsible way to use AI and respect other's time.

## Usage

```
/ai-declare copilot   # declare at `copilot` level
/ai-declare           # let the agent guide you through the declaration
```

The skill creates/updates `AI-DECLARATION.md` and adds a badge to your `README.md`.

## Levels

| Level | Meaning |
|-------|---------|
| `none` | Human acts alone; no AI involvement |
| `hint` | AI surfaces suggestions passively |
| `assist` | Human prompts; AI acts on a part of the task |
| `pair` | Both act equally; Human understands internals |
| `copilot` | AI acts on the whole task, prompting Human for permission |
| `auto` | AI acts autonomously to completion |

## Reference

- [AI-DECLARATION.md spec](https://ai-declaration.md)
- [Spec repo](https://github.com/DimwitLabs/AI-DECLARATION.md)
