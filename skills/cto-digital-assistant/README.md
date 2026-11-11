# CTO Digital Assistant

> **Skill name:** `cto-digital-assistant` · **Plugin:** `product-leadership-skills` · **Instructions:** [`SKILL.md`](SKILL.md)

## Overview

A senior digital assistant to the CTO of a fintech AI company. It reads codebases and audits them against PRDs, SRS documents, RFCs and roadmaps, producing **implementation fidelity reports** (built correctly / diverged / missing), velocity-driven milestones, sprint plans, tech-debt assessments, decision support and board/investor updates — benchmarked against Stripe-, Plaid- and Flutterwave-scale standards. It tells you what's true, not what you want to hear.

## When to use it

- Measuring how much of the PRD is actually built
- Preparing sprint plans, milestones and quarterly roadmaps
- Board or investor engineering updates
- High-impact technical decisions: build vs buy, vendor selection, stack choices, scaling strategy
- Engineering health: velocity, tech debt, hiring evaluation

## What to give it

- Codebase(s)
- Product documents: PRD, SRS, RFCs, roadmap, specs
- Optional: team size, deadlines, current sprint, business goals

## What you get back

- Implementation Fidelity Report with a /100 fidelity score and an unfiltered CTO assessment
- Feature registry: ✅ correctly implemented, ⚠️ incorrectly implemented, ❌ not implemented (prioritised P0–P2)
- Milestones and sprint plans tied to velocity
- Decision memos, risk registers, board-ready summaries

## Example prompts

**1. Fidelity report**

```text
Audit this codebase against docs/PRD.md and docs/SRS.md. What's built correctly, what diverged, what's missing? Give me a fidelity score.
```

**2. Milestones**

```text
Based on what's implemented and a team of 6 engineers, set milestones for the next two sprints to hit the Q1 launch.
```

**3. Board update**

```text
Prepare a one-page engineering update for the board: progress vs roadmap, key risks, and what we need.
```

**4. Decision support**

```text
Should we build our own e-invoicing integration with FIRS or use a third-party provider? Give me a decision memo.
```

**5. Tech debt**

```text
Measure our technical debt in apps/api and rank the items by business risk.
```

**6. Architecture review**

```text
Review our architecture for 10× transaction volume by next year. Where does it break first?
```

**7. Hiring**

```text
Here's a take-home submission for a senior backend role. Evaluate it against our bar.
```

## How to invoke

| Where | How |
|---|---|
| Claude Code CLI | Run `claude` in your repo and type any prompt above, or use the slash command `/cto-digital-assistant` (plugin install: `/product-leadership-skills:cto-digital-assistant`) |
| Claude Code Desktop | Code tab → open your project folder → type the prompt, or say *"Use the cto-digital-assistant skill to…"* |
| Claude app | Upload this folder as a `.zip` under Settings → Capabilities → Skills, then prompt normally |
| Headless / CI | `claude -p "Use the cto-digital-assistant skill to …"` |

## Related skills

- `senior-qa-engineer` — focused release verdict
- `fintech-product-manager-developer-ticket-creator` — convert gaps into tickets

---

[← Back to all skills](../../README.md)
