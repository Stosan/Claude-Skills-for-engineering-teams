# Senior AI/ML Engineering Assistant

> **Skill name:** `senior-aiml-engineering-assistant` · **Plugin:** `engineering-skills` · **Instructions:** [`SKILL.md`](SKILL.md)

## Overview

A combined Senior Software Engineer and Senior AI/ML Engineer that **executes** in your repo across TypeScript/Node.js, Go, Python and C++. It follows a strict sequence: read rules & memory → orient in the codebase → engineering brief for your approval → tests first → surgical implementation → update project memory and flag breaking changes.

## When to use it

- Building a feature or endpoint end-to-end
- Fixing a bug with a regression test
- Refactoring safely
- Adding AI/ML capabilities (inference pipelines, RAG, classifiers, evals)
- Performance optimisation

## What to give it

- A task description (feature, bug, refactor)
- The repository opened in Claude Code
- Optional: existing `AIAgentRule.md` and `codebaseMemory.md` in the repo root

## What you get back

- An **Engineering Brief** you approve before any code is written
- Tests written before implementation
- Production-grade code changes
- `AIAgentRule.md` (created if missing), `codebaseMemory.md` (updated after every change), `humanReview.md` (breaking changes flagged for a human)

> **Note:** This skill **writes code and files**. Review its brief, the diffs and `humanReview.md` before committing.

## Example prompts

**1. Feature**

```text
Add idempotency keys to POST /payments. Show me the engineering brief first, write the tests, then implement.
```

**2. Bug fix**

```text
Fix the bug where VAT is double-counted on credit notes in src/tax/vat.ts. Add a regression test.
```

**3. Refactor**

```text
Refactor the filing service into a repository + service layer without changing behaviour. Flag anything that could break callers.
```

**4. AI/ML**

```text
Add a document-classification step that tags uploaded receipts as VAT-eligible or not, with an eval set and confidence threshold.
```

**5. Go service**

```text
Implement graceful shutdown and context cancellation across the Go ledger service.
```

**6. Performance**

```text
The /reports endpoint takes 8s. Profile it, find the N+1 queries and fix them with tests proving the improvement.
```

**7. Memory**

```text
Read codebaseMemory.md and tell me what was changed in the last session before we continue.
```

## How to invoke

| Where | How |
|---|---|
| Claude Code CLI | Run `claude` in your repo and type any prompt above, or use the slash command `/senior-aiml-engineering-assistant` (plugin install: `/engineering-skills:senior-aiml-engineering-assistant`) |
| Claude Code Desktop | Code tab → open your project folder → type the prompt, or say *"Use the senior-aiml-engineering-assistant skill to…"* |
| Claude app | Upload this folder as a `.zip` under Settings → Capabilities → Skills, then prompt normally |
| Headless / CI | `claude -p "Use the senior-aiml-engineering-assistant skill to …"` |

## Related skills

- `brainstorming-colleague` — think through the design first
- `ai-code-reviewer` / `staff-engineer-production-review` — review the result

---

[← Back to all skills](../../README.md)
