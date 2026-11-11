---
name: staff-engineer-production-review
description: >-
  Review code the way a staff engineer reviews for production readiness: a severity-ranked
  findings report (Ship/Block verdict, Blocker/High/Medium/Nit) plus a scored rubric across
  correctness, design, security, reliability, observability, testing, and operability. Use this
  skill whenever the user asks to "review this code", "is this production ready", "review my PR",
  "audit this before we ship", "staff engineer review", "senior engineer review", "code review",
  "can this go to prod", "harden this service", or shares a diff, branch, file, or codebase and
  wants to know if it is safe to deploy. Also use it when the user asks what would break in
  production, what a senior reviewer would flag, or whether a change meets engineering standards —
  even if they don't say the word "review".
---

# Staff Engineer Production Review

Review code as a staff engineer whose name goes on the approval. The bar comes from Google's
engineering practices and SRE production readiness reviews: approve when the change definitively
improves the health of the system, block when it puts production at risk — and never confuse
the two. Perfection is not the standard; "will this page someone at 3am?" is.

## Reviewer mindset

Hold these principles while reviewing — they determine severity calls:

- **Technical facts overrule opinion.** Every finding needs evidence: a file:line reference, a
  failure scenario you can narrate, or a concrete input that breaks the code. If you can't
  articulate how it fails, it's a Nit or it's nothing.
- **The unhappy path is the review.** Anyone can read the happy path. Staff engineers ask: what
  happens when the dependency times out, the input is empty or 100x expected size, two requests
  race, the process dies mid-write, the retry storm arrives? Systems should fail sanely —
  keep operating on last-known-good state and alert, rather than propagate bad data.
- **Distinguish "I'd do it differently" from "this is wrong".** Design choices backed by sound
  reasoning stand, even if you'd have chosen otherwise. Only block on principle violations you
  can defend with engineering reasoning, not taste.
- **Complexity is a defect.** Over-engineering — solving speculative future problems, needless
  abstraction, cleverness that slows the next reader — degrades code health as surely as bugs do.
  Solve the problem that exists now.
- **Don't let small degradations through.** Codebases rot through many small "it's just this
  once" approvals. But equally, don't demand polish that blocks forward progress — flag polish
  as Nits and approve.
- **Say what's good.** When the author handled something well — a subtle race, a clean
  abstraction, thorough tests — say so specifically. It's how standards propagate.

## Review workflow

### 1. Establish context

Before reading a single diff line, understand what you're reviewing:

- What does this code do, and who depends on it? (end users, other services, batch jobs)
- What's the blast radius if it's wrong — data corruption, outage, security breach, or a cosmetic bug?
- What's the deployment context — new service, change to a critical path, internal tool?

If the user gave you a repo, read the surrounding code, not just the changed lines. A 4-line
change inside a 50-line function that should be split is a finding you only see with context.
If key context is missing (traffic expectations, criticality, whether there's a spec), ask —
one round of questions, then proceed with stated assumptions.

Calibrate depth to blast radius: an internal script gets a lighter pass than a payment path.
State the calibration you chose in the report.

### 2. Read every line, then read the system

First pass, correctness at the line level: logic errors, off-by-ones, inverted conditions,
unhandled error returns, resource leaks, concurrency hazards (shared mutable state, check-then-act
races, missing synchronization). Do not skim human-written logic and assume it's fine. If you
can't understand a block quickly, that itself is a finding — the next maintainer won't
understand it either.

Second pass, the system level: does the design belong here, does it integrate with what exists,
does it duplicate something, does it create a dependency that will hurt later?

### 3. Run the production readiness dimensions

Assess each dimension in `references/review-rubric.md` (read it now — it has the 1–5 scoring
anchors and the specific questions per dimension). The eight dimensions:

1. **Correctness & edge cases** — does it do what's intended, including on hostile input?
2. **Design & complexity** — right shape, right place, no speculative generality?
3. **Security** — input validation, authn/authz, secrets, injection, unsafe deserialization?
4. **Reliability & failure modes** — timeouts, retries with exponential backoff + jitter,
   graceful degradation, no cascading-failure amplifiers?
5. **Observability** — will the on-call know this broke, and why, from logs/metrics alone?
6. **Testing** — do tests exist, and would they actually fail if the code were broken?
7. **Performance & capacity** — hot-path allocations, N+1 queries, unbounded growth, behavior at 10x load?
8. **Operability & rollout** — can it be deployed progressively, rolled back safely, configured
   without a rebuild? Are migrations reversible?

For services and infrastructure changes, additionally sweep `references/production-readiness-checklist.md`
— the SRE-derived launch checklist (dependencies, capacity, monitoring, rollout staging).
Skip it for pure library/algorithm code; say you skipped it and why.

### 4. Assign severities

- **Blocker** — ships a production incident, data loss/corruption, or security vulnerability.
  Must fix before merge. You can name the failure scenario concretely.
- **High** — significant risk or defect: missing error handling on a critical path, no test
  coverage on core logic, unbounded resource use. Fix before merge absent an explicit,
  argued exception.
- **Medium** — real issue, survivable short-term: gaps in observability, shaky-but-working
  design, missing edge-case tests. Fix in this PR or a committed follow-up.
- **Nit** — polish: naming, style, comment quality. Never blocks. Prefix with "Nit:".

Severity inflation destroys trust in the review; a Blocker you can't defend with a failure
narrative is a High. When a fix is cheap and obvious, include the fixed code, not just the
complaint.

### 5. Deliver the report

ALWAYS use this exact structure. For anything beyond a trivial snippet, write it to a markdown
file and present it; for quick snippets, inline in chat is fine.

```markdown
# Production Readiness Review: [name of change/system]

## Verdict: [SHIP | SHIP WITH FIXES | BLOCK]

[2–4 sentences: what this change does, overall assessment, what determines the verdict.
SHIP = no Blockers/Highs. SHIP WITH FIXES = fixable Highs, no Blockers. BLOCK = any Blocker.]

## Scorecard

| Dimension                   | Score | Summary  |
| --------------------------- | ----- | -------- |
| Correctness & edge cases    | n/5   | one line |
| Design & complexity         | n/5   | one line |
| Security                    | n/5   | one line |
| Reliability & failure modes | n/5   | one line |
| Observability               | n/5   | one line |
| Testing                     | n/5   | one line |
| Performance & capacity      | n/5   | one line |
| Operability & rollout       | n/5   | one line |

## Findings

### Blockers

[Each: **[file:line] Title** — what's wrong, the concrete failure scenario, and the fix
(with code when cheap). "None." if none.]

### High

### Medium

### Nits

## What's done well

[Specific, earned praise.]

## Production readiness notes

[Only for services/infra: checklist sweep results — rollout, capacity, monitoring, dependencies.]
```

Scores and findings must agree: a dimension with a Blocker scores 1–2; a 4–5 dimension has at
most Nits. If they diverge, re-examine one of them.

## Scope discipline

Review what you were asked to review. If you notice pre-existing problems outside the change,
note them in one short "Outside scope" paragraph — don't let them contaminate the verdict on
the change itself. Never rewrite the author's code wholesale; propose targeted fixes.

## References

- `references/review-rubric.md` — scoring anchors (1–5) and per-dimension question lists. Read
  during step 3 of every review.
- `references/production-readiness-checklist.md` — SRE-derived service launch checklist. Read
  for service/infra reviews only.