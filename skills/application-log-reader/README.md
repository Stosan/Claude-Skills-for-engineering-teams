# Application Log Reader

> **Skill name:** `application-log-reader` · **Plugin:** `engineering-skills` · **Instructions:** [`SKILL.md`](SKILL.md)

## Overview

Forensic debugging from logs, stack traces and crash reports — GCP Cloud Run, Docker, Kubernetes, VMs or local. When the codebase is available it correlates log lines with source to pinpoint the exact file, function and execution path, then prescribes precise fixes with verification steps and side effects.

## When to use it

- Production incidents and on-call triage
- Unexplained 500s, crashes, timeouts, OOMs
- Interpreting a long stack trace
- Root-causing intermittent failures across services

## What to give it

- Logs: pasted, a file path, or piped in
- The codebase (strongly recommended — enables file/function-level pinpointing)
- Optional: time window, deployment/runtime, recent changes

## What you get back

- Executive summary of what failed and why
- Error isolation & classification (five-pass read)
- Exact location: file → function → call chain
- Root cause analysis
- Fix prescriptions: current vs fixed code, why it works, verification, side effects, and a fallback if the fix can't ship immediately

## Example prompts

**1. Incident**

```text
Our Cloud Run service started returning 500s at 14:05. Here are the logs. Find the root cause in this repo and tell me how to fix it.
```

**2. Stack trace**

```text
Trace this stack trace to the exact line in our codebase and explain what triggered it: <paste>
```

**3. Kubernetes**

```text
Pods in the tax-engine deployment keep restarting with OOMKilled. Here are `kubectl logs` and `describe` outputs.
```

**4. Log file**

```text
Analyze ./logs/api-2026-11-10.log — what are the top errors, when did they start, and which one matters most?
```

**5. Intermittent**

```text
Every few hours payments webhook processing fails with 'context deadline exceeded'. Logs attached. What's going on?
```

**6. Piped**

```text
cat worker.log | claude -p "Use application-log-reader. Find the root cause and give me the fix."
```

## How to invoke

| Where | How |
|---|---|
| Claude Code CLI | Run `claude` in your repo and type any prompt above, or use the slash command `/application-log-reader` (plugin install: `/engineering-skills:application-log-reader`) |
| Claude Code Desktop | Code tab → open your project folder → type the prompt, or say *"Use the application-log-reader skill to…"* |
| Claude app | Upload this folder as a `.zip` under Settings → Capabilities → Skills, then prompt normally |
| Headless / CI | `claude -p "Use the application-log-reader skill to …"` |

## Related skills

- `senior-aiml-engineering-assistant` — implement the prescribed fix with tests

---

[← Back to all skills](../../README.md)
