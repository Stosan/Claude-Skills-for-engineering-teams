# Staff Engineer Production Review

> **Skill name:** `staff-engineer-production-review` · **Plugin:** `engineering-skills` · **Instructions:** [`SKILL.md`](SKILL.md)

## Overview

Reviews code the way a staff engineer gates production: a clear **SHIP / SHIP WITH FIXES / BLOCK** verdict, severity-ranked findings (Blocker / High / Medium / Nit) and a scored rubric across correctness, design, security, reliability, observability, testing and operability.

## When to use it

- Before merging a PR or deploying a branch
- Hardening a service before launch
- Getting a second senior opinion on a design-heavy change
- Asking 'what would break in production?'

## What to give it

- A diff, branch, PR, file(s) or service directory
- Context on what the change is for and how it's deployed (optional but valuable)

## What you get back

- Verdict: SHIP / SHIP WITH FIXES / BLOCK
- Scorecard across production-readiness dimensions
- Findings grouped as Blockers, High, Medium, Nits — each with location, impact and fix
- What's done well, production-readiness notes and scope-discipline notes

## Example prompts

**1. PR review**

```text
Review my PR on this branch vs main. Can this go to prod?
```

**2. Single service**

```text
Do a staff engineer review of services/filing-gateway. Focus on reliability, retries and observability.
```

**3. What would break**

```text
What would break in production if we deployed apps/api as it is today?
```

**4. Security-focused**

```text
Review the auth middleware in src/middleware/auth.ts like a staff engineer would — security and failure modes first.
```

**5. Hardening**

```text
Harden the reconciliation worker: run a production-readiness review and list blockers before our launch on Friday.
```

**6. Slash command**

```text
/staff-engineer-production-review review the last 3 commits
```

**7. CI usage**

```text
claude -p "Use staff-engineer-production-review on the diff vs origin/main. Print only the verdict and Blockers."
```

## How to invoke

| Where | How |
|---|---|
| Claude Code CLI | Run `claude` in your repo and type any prompt above, or use the slash command `/staff-engineer-production-review` (plugin install: `/engineering-skills:staff-engineer-production-review`) |
| Claude Code Desktop | Code tab → open your project folder → type the prompt, or say *"Use the staff-engineer-production-review skill to…"* |
| Claude app | Upload this folder as a `.zip` under Settings → Capabilities → Skills, then prompt normally |
| Headless / CI | `claude -p "Use the staff-engineer-production-review skill to …"` |

## Related skills

- `ai-code-reviewer` — when the code was AI-generated
- `audit-and-remediate` — scores an entire codebase rather than a change

---

[← Back to all skills](../../README.md)
