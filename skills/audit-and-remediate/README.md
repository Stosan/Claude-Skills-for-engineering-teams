# Audit & Remediate (AI-Fintech Edition)

> **Skill name:** `audit-and-remediate` · **Plugin:** `engineering-skills` · **Instructions:** [`SKILL.md`](SKILL.md)

## Overview

A structured engineering audit for AI-fintech services in Go, TypeScript/Node.js, Python, Rust and C++. Scores the codebase 0–10 across six dimensions with fintech-specific precision (money math, idempotency, double-spend, PII, KYC/AML, CBN / PCI-DSS / NDPR posture) and produces a sequenced remediation roadmap with exact `file:line` citations. **Read-only: it never modifies code.**

## When to use it

- Quarterly or pre-launch health checks of a service
- Due diligence before scaling a system or taking on a new codebase
- Building a prioritised hardening backlog for the team
- Compliance readiness reviews

## What to give it

- The service or repository (or a list of services)
- Optional: areas of concern, compliance targets, upcoming deadlines

## What you get back

- `AUDIT.md`-style scored report across: Correctness, Structure, Performance, Testing, Production Readiness, Senior/Enterprise
- Findings with `file:line` citations
- Sequenced remediation roadmap in batches the team can execute
- Implementation-ready fix prescriptions (no code is changed)

## Example prompts

**1. Full audit**

```text
Audit the services/ledger Go service and score it across all six dimensions. Give me a remediation roadmap with file:line citations.
```

**2. Multi-service**

```text
Audit api-gateway (Node/TS), tax-engine (Python) and ledger (Go). Compare scores and tell me which to fix first.
```

**3. Money safety**

```text
Audit our codebase specifically for float arithmetic on money, missing idempotency and double-spend vectors.
```

**4. Compliance posture**

```text
Assess this repo's NDPR and PCI-DSS posture: PII in logs, secrets handling, audit trails. Prescribe fixes.
```

**5. AI pipeline**

```text
Audit the document-classification inference service — observability, model versioning, latency and failure handling.
```

**6. Backlog creation**

```text
Harden this codebase: audit it, then turn the roadmap into batches my team can pick up over the next 3 sprints.
```

## How to invoke

| Where | How |
|---|---|
| Claude Code CLI | Run `claude` in your repo and type any prompt above, or use the slash command `/audit-and-remediate` (plugin install: `/engineering-skills:audit-and-remediate`) |
| Claude Code Desktop | Code tab → open your project folder → type the prompt, or say *"Use the audit-and-remediate skill to…"* |
| Claude app | Upload this folder as a `.zip` under Settings → Capabilities → Skills, then prompt normally |
| Headless / CI | `claude -p "Use the audit-and-remediate skill to …"` |

## Related skills

- `staff-engineer-production-review` — for an individual change
- `fintech-product-manager-developer-ticket-creator` — turn findings into Linear tickets

---

[← Back to all skills](../../README.md)
