# Senior QA Engineer

> **Skill name:** `senior-qa-engineer` · **Plugin:** `engineering-skills` · **Instructions:** [`SKILL.md`](SKILL.md)

## Overview

A senior QA engineer embedded in your codebase. It traces every PRD requirement to its implementation, walks user journeys end-to-end, hunts regressions and breaking changes, and checks production readiness. **Investigates exhaustively, reports concisely** — the final report fits on one screen.

## When to use it

- Before a release, to get a go/no-go
- Checking whether 'done' features are really implemented end-to-end
- Verifying a branch against a PRD, SRS or spec
- Hunting breaking changes after a refactor or dependency upgrade

## What to give it

- The codebase (or a branch / folder)
- The PRD / SRS / spec / acceptance criteria — the source of truth
- Optional: baseline branch for breaking-change comparison

## What you get back

- **VERDICT**: 🔴 BLOCKED / 🟡 CONDITIONAL PASS / 🟢 SHIP
- 🔴 Blockers, 🟠 Should-fix, 🟡 Notes — a concise defect log
- Per-requirement pass/fail traceability
- Release gate checklist

## Example prompts

**1. Full QA pass**

```text
QA this codebase against docs/PRD-filing-v2.md. Is everything implemented end-to-end? Give me the release verdict.
```

**2. Breaking changes**

```text
We upgraded to Prisma 6 and refactored the user module on this branch. Check for breaking changes vs main.
```

**3. Feature verification**

```text
Did we build the VAT remittance flow correctly? Here are the acceptance criteria: <paste>
```

**4. Release gate**

```text
Is release/1.8 release-ready? Gate it.
```

**5. Specific journey**

```text
Walk the 'business onboarding → TIN verification → first filing' journey end-to-end in the code and tell me where it breaks.
```

**6. PM-friendly**

```text
I'm a PM — in plain language, which features from our PRD are fully built, partially built, or missing?
```

## How to invoke

| Where | How |
|---|---|
| Claude Code CLI | Run `claude` in your repo and type any prompt above, or use the slash command `/senior-qa-engineer` (plugin install: `/engineering-skills:senior-qa-engineer`) |
| Claude Code Desktop | Code tab → open your project folder → type the prompt, or say *"Use the senior-qa-engineer skill to…"* |
| Claude app | Upload this folder as a `.zip` under Settings → Capabilities → Skills, then prompt normally |
| Headless / CI | `claude -p "Use the senior-qa-engineer skill to …"` |

## Related skills

- `cto-digital-assistant` — broader fidelity report with milestones and leadership framing
- `staff-engineer-production-review` — code-quality gate on a specific change

---

[← Back to all skills](../../README.md)
