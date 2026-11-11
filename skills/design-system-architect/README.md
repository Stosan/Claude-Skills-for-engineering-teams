# Design System Architect

> **Skill name:** `design-system-architect` · **Plugin:** `product-leadership-skills` · **Instructions:** [`SKILL.md`](SKILL.md)

## Overview

Two modes. **Create** builds a complete, documented design system (brand fundamentals, colour, type, spacing, radius, elevation, motion tokens, components and UI-kit previews) from whatever exists — brand files, Figma, decks, a codebase, a website or just a description. **Review** audits an existing codebase or live URL, extracts the design system it actually uses, scores it with evidence and prescribes a better one.

## When to use it

- Starting a new product or rebrand
- Inconsistent UI across apps (too many greys, ad-hoc spacing, duplicate buttons)
- Before a frontend rewrite or migration to a component library
- Aligning design and engineering on tokens

## What to give it

- Create: brand assets, Figma links, existing site/codebase, or a product description
- Review: a repo/folder (e.g. `apps/web`) or live URL(s)

## What you get back

- Brand book (fundamentals, foundations, components)
- Token files: `tokens.css`, `tokens.json`
- Component specs and previews
- Review mode: scored audit (0–5 per area) with findings and a prescribed system

## Example prompts

**1. Create from scratch**

```text
Create a design system for TaxStreem: a trustworthy, modern fintech for Nigerian SMEs. Include tokens and core components.
```

**2. Review a codebase**

```text
Review the design system apps/web actually uses, score it, and prescribe a cleaner token set and component list.
```

**3. Review a URL**

```text
Audit https://example.com and tell me what design choices to improve, with evidence.
```

**4. Review then create**

```text
Audit our dashboard's current UI, then create an improved design system based on the findings.
```

**5. Tokens only**

```text
Extract our colour, type and spacing into tokens.css and tokens.json from the existing Tailwind config.
```

**6. Dark mode**

```text
Extend our design system with a dark theme that keeps WCAG AA contrast.
```

## How to invoke

| Where | How |
|---|---|
| Claude Code CLI | Run `claude` in your repo and type any prompt above, or use the slash command `/design-system-architect` (plugin install: `/product-leadership-skills:design-system-architect`) |
| Claude Code Desktop | Code tab → open your project folder → type the prompt, or say *"Use the design-system-architect skill to…"* |
| Claude app | Upload this folder as a `.zip` under Settings → Capabilities → Skills, then prompt normally |
| Headless / CI | `claude -p "Use the design-system-architect skill to …"` |

## Related skills

- `brainstorming-colleague` — explore product direction before designing

---

[← Back to all skills](../../README.md)
