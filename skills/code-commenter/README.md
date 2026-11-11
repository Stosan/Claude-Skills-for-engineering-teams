# Code Commenter

> **Skill name:** `code-commenter` · **Plugin:** `engineering-skills` · **Instructions:** [`SKILL.md`](SKILL.md)

## Overview

Adds docstrings and inline comments in the voice of a thoughtful senior engineer speaking to the next developer — explaining not just *what* the code does but *why*, what was considered and rejected, which ordering is load-bearing, what external contracts force a design, and what must not break. **It illuminates code; it never rewrites it.**

## When to use it

- Onboarding new engineers to a complex module
- Documenting tricky business logic (tax rules, reconciliation, money math)
- Before handing a codebase to another team
- Making a legacy file understandable

## What to give it

- A file, module, class or function
- Optional: background on why it was built that way (Claude will ask if the intent is unclear)

## What you get back

- Docstrings following the language's conventions (JSDoc, Go doc, Python docstrings, Doxygen…)
- Inline comments explaining intent, rejected alternatives, ordering constraints and external requirements
- 'Future note' comments marking assumptions that may change
- No changes to logic

## Example prompts

**1. Single file**

```text
Add docstrings and inline comments to src/tax/vat_calculator.py so a new engineer understands why it works this way.
```

**2. Onboarding**

```text
A junior dev joins Monday and owns services/reconciliation. Comment it so they can understand and safely change it.
```

**3. Why, not what**

```text
Document why this retry logic in webhook_client.go is structured this way — what would break if someone changed it?
```

**4. Docstrings only**

```text
Add docstrings to every exported function in packages/sdk/src/ — no inline comments.
```

**5. Regulatory logic**

```text
Comment the PAYE band calculation and explain which parts are dictated by the Nigeria Tax Act 2025 rather than our own design.
```

**6. Legacy**

```text
This file has zero comments and the author left. Annotate it and flag anything you can't explain from the code alone.
```

## How to invoke

| Where | How |
|---|---|
| Claude Code CLI | Run `claude` in your repo and type any prompt above, or use the slash command `/code-commenter` (plugin install: `/engineering-skills:code-commenter`) |
| Claude Code Desktop | Code tab → open your project folder → type the prompt, or say *"Use the code-commenter skill to…"* |
| Claude app | Upload this folder as a `.zip` under Settings → Capabilities → Skills, then prompt normally |
| Headless / CI | `claude -p "Use the code-commenter skill to …"` |

## Related skills

- `senior-aiml-engineering-assistant` — if the code needs changes, not just comments

---

[← Back to all skills](../../README.md)
