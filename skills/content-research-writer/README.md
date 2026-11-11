# Content Research Writer

> **Skill name:** `content-research-writer` · **Plugin:** `product-leadership-skills` · **Instructions:** [`SKILL.md`](SKILL.md)

## Overview

A research-first, authority-driven writing engine for articles, blog posts, LinkedIn posts, newsletters, technical deep-dives, thought leadership and whitepapers. It asks about audience and platform first, researches sources published **2024 or later**, develops a thesis and outline, then drafts in the voice of a senior AI/ML and infrastructure engineer — with sources, research notes and revision options.

## When to use it

- Engineering blog posts and technical deep-dives
- LinkedIn thought leadership for founders and leaders
- Newsletters, whitepapers, conference abstracts
- Turning an internal project into a public write-up

## What to give it

- Topic, title or rough idea
- Answers to intake questions: audience, platform, angle/thesis, tone, length, anything to avoid

## What you get back

- Researched sources (2024+) with notes
- Thesis and outline
- Full draft formatted for the target platform
- Sources used, research notes, revision options, optional SEO and series ideas

> **Note:** Expect a short set of intake questions before drafting — answering them up front saves a round-trip.

## Example prompts

**1. LinkedIn**

```text
Write a LinkedIn article on how we use LLMs to automate tax compliance checks. Audience: fintech CTOs.
```

**2. Engineering blog**

```text
Research and write a technical deep-dive on building idempotent payment webhooks in Go for our engineering blog.
```

**3. Thought leadership**

```text
Draft a thought-leadership piece on what the Nigeria Tax Act 2025 means for fintech infrastructure.
```

**4. Newsletter**

```text
Create this month's engineering newsletter from these release notes and incidents: <paste>
```

**5. Whitepaper**

```text
Write a whitepaper on AI-assisted tax filing accuracy for an investor audience.
```

**6. Series**

```text
Plan a 4-part blog series on our journey from monolith to event-driven architecture, then draft part 1.
```

## How to invoke

| Where | How |
|---|---|
| Claude Code CLI | Run `claude` in your repo and type any prompt above, or use the slash command `/content-research-writer` (plugin install: `/product-leadership-skills:content-research-writer`) |
| Claude Code Desktop | Code tab → open your project folder → type the prompt, or say *"Use the content-research-writer skill to…"* |
| Claude app | Upload this folder as a `.zip` under Settings → Capabilities → Skills, then prompt normally |
| Headless / CI | `claude -p "Use the content-research-writer skill to …"` |

## Related skills

- `brainstorming-colleague` — develop the angle first

---

[← Back to all skills](../../README.md)
