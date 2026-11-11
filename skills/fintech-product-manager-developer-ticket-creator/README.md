# Fintech PM → Developer Ticket Creator

> **Skill name:** `fintech-product-manager-developer-ticket-creator` · **Plugin:** `product-leadership-skills` · **Instructions:** [`SKILL.md`](SKILL.md)

## Overview

Turns messy input — Slack threads, email chains, meeting notes, support tickets, codebase-review findings — into polished, compliance-aware **Linear tickets** for Nigerian tax and compliance products. It automatically detects implications under the Nigeria Tax Act 2025, FIRS requirements, CIT, PIT/PAYE, VAT, stamp duties and filing deadlines.

## When to use it

- After a Slack discussion or meeting that produced action items
- Converting support escalations into engineering work
- Turning audit/QA findings into a backlog
- Any ticket that touches tax computation, filing or regulatory deadlines

## What to give it

- Raw text: Slack thread, email, meeting notes, bug report, review findings
- Optional: priority, team, related tickets, deadline

## What you get back

- Linear-ready ticket(s) with: Problem Statement, Current State, Desired State, Business Impact
- **Compliance & Regulatory Context** (NTA 2025, FIRS, CIT/PIT/VAT, deadlines)
- Technical Considerations, Acceptance Criteria, Risks & Mitigations, Dependencies, Success Metrics

> **Note:** Combine with a Linear connector in Claude to create the tickets directly instead of copy-pasting.

## Example prompts

**1. Slack thread**

```text
Turn this Slack thread into a Linear ticket: <paste thread>
```

**2. Meeting notes**

```text
Here are notes from today's product sync. Create one ticket per action item with acceptance criteria: <paste notes>
```

**3. Support escalation**

```text
A customer says their VAT return shows the wrong output tax for credit notes. Write a bug ticket with compliance impact.
```

**4. Deadline-aware**

```text
We need monthly PAYE remittance reminders before the statutory deadline. Write the feature ticket and flag the regulatory dates.
```

**5. From audit findings**

```text
Convert these audit-and-remediate findings into prioritised Linear tickets: <paste findings>
```

**6. Epic breakdown**

```text
Break 'support the new stamp duty rules' into an epic with sub-tickets for backend, frontend and QA.
```

## How to invoke

| Where | How |
|---|---|
| Claude Code CLI | Run `claude` in your repo and type any prompt above, or use the slash command `/fintech-product-manager-developer-ticket-creator` (plugin install: `/product-leadership-skills:fintech-product-manager-developer-ticket-creator`) |
| Claude Code Desktop | Code tab → open your project folder → type the prompt, or say *"Use the fintech-product-manager-developer-ticket-creator skill to…"* |
| Claude app | Upload this folder as a `.zip` under Settings → Capabilities → Skills, then prompt normally |
| Headless / CI | `claude -p "Use the fintech-product-manager-developer-ticket-creator skill to …"` |

## Related skills

- `audit-and-remediate`, `senior-qa-engineer`, `cto-digital-assistant` — produce findings to feed into tickets

---

[← Back to all skills](../../README.md)
