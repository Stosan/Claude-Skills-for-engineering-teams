# Brainstorming Colleague

> **Skill name:** `brainstorming-colleague` · **Plugin:** `product-leadership-skills` · **Instructions:** [`SKILL.md`](SKILL.md)

## Overview

A deep thinking partner for complex, ambiguous or high-stakes problems — not a Q&A bot. It decomposes the problem, challenges the framing, reasons through multiple lenses, generates 3–5 solution paths, pushes back when needed, maps systems and edge cases, and **always closes with a concrete implementation plan**.

## When to use it

- System design and architecture decisions
- Product strategy, startup ideation, growth plans
- AI agent architecture and research planning
- 'I'm stuck' or 'poke holes in this' moments

## What to give it

- The problem, idea or decision — rough is fine
- Constraints you know about (team, time, budget, regulation)

## What you get back

- Problem decomposition and reframing
- Multiple solution paths with explicit trade-offs and a recommendation
- Diagrams, trade-off tables or structured reasoning blocks
- **Implementation Plan**: objective, assumptions, critical path, phases, parallel workstreams, decisions required

## Example prompts

**1. Architecture**

```text
Help me think through moving tax computation from synchronous API calls to an event-driven pipeline. Challenge my assumptions.
```

**2. Poke holes**

```text
Here's our plan to launch e-invoicing in 6 weeks. Poke holes in it: <paste plan>
```

**3. Product strategy**

```text
I'm trying to figure out whether we should target accountants or SMEs directly first. Think through it with me.
```

**4. AI agents**

```text
Help me architect an AI agent that reconciles bank statements against filed returns. What am I missing?
```

**5. Stuck**

```text
I'm stuck on how to version tax rules so historical filings stay reproducible. Let's explore options.
```

**6. Decision**

```text
Postgres vs. a dedicated ledger DB for our double-entry ledger — what would you do and why?
```

## How to invoke

| Where | How |
|---|---|
| Claude Code CLI | Run `claude` in your repo and type any prompt above, or use the slash command `/brainstorming-colleague` (plugin install: `/product-leadership-skills:brainstorming-colleague`) |
| Claude Code Desktop | Code tab → open your project folder → type the prompt, or say *"Use the brainstorming-colleague skill to…"* |
| Claude app | Upload this folder as a `.zip` under Settings → Capabilities → Skills, then prompt normally |
| Headless / CI | `claude -p "Use the brainstorming-colleague skill to …"` |

## Related skills

- `senior-aiml-engineering-assistant` — execute the plan
- `cto-digital-assistant` — turn the plan into milestones

---

[← Back to all skills](../../README.md)
