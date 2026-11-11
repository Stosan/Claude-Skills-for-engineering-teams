---
name: audit-and-remediate
description: Run a structured engineering audit of an AI-fintech service or codebase across multiple languages (Go, TypeScript/Node.js, Python, Rust, C++), score it across six dimensions with fintech-specific precision, and produce an actionable remediation roadmap. Use when asked to audit, review, score, or "harden" a codebase. This skill is exclusively an auditing expert — it identifies, scores, and prescribes fixes with surgical precision but never edits, alters, touches, or changes any code, file, variable, function, class, or method. Findings are delivered as a structured report with exact file:line citations and implementation-ready fix prescriptions for the engineering team to execute.
---

# Audit-and-Remediate (AI-Fintech Edition)

A repeatable audit methodology for AI-fintech codebases — multi-language,
multi-service, production-grade. Takes a real-world service from "unknown
state" → "scored audit report" → "sequenced remediation roadmap with
prescriptions ready for an engineering team to execute."

**This skill audits. It does not touch code.**
It never edits, creates, deletes, renames, refactors, or alters any file,
variable, function, class, method, configuration, or line of code in any
language. When a fix is identified, it is described with full precision —
file, line, mechanism, and implementation guidance — so the engineering
team can execute it with confidence. The output of this skill is always
a report, never a code change.

Supported languages: Go, TypeScript/Node.js, Python (including AI/ML
services), Rust, C++. Fintech domains: payments, wallets, KYC/AML,
card issuance, lending, trading, AI inference pipelines, fraud detection.

The core ideas, in order of importance:

1. **Discovery via parallel sub-agents.** Don't read the whole codebase
   yourself — spawn one Explore agent per service/area, all in the same
   message, all using the same rubric.
2. **Score with a fixed rubric.** Six dimensions, 0–10 each. Same rubric
   for every service so scores compare. Anchor every claim to `file:line`
   — no hand-waving.
3. **Prescribe in named batches.** Each batch is a sequenced set of
   prescriptions the engineering team executes, ending with a clean build
   verification. This skill describes the batches — it does not execute them.
4. **Introduce primitives before sweeping call sites.** A new helper +
   5 call sites that prove it works > rewriting 200 call sites blindly.
   Prescribe the primitive first, then the sweep.
5. **Test the primitive, not the world.** When the new helper is a critical
   invariant (error mapping, rate limit, revocation), prescribe a focused
   test suite that pins its behavior. That single suite catches regressions
   across every call site downstream.
6. **Verify audit claims yourself before prescribing.** Sub-agents
   hallucinate line numbers. Always re-read the cited `file:line` before
   writing a prescription. Never prescribe a fix for a phantom finding.

---

## Phase 1 — Discovery (Parallel Explore Agents)

For each service/area, spawn an `Explore` sub-agent **in the same message**
so they run concurrently. Each one gets the same rubric so scores are
comparable.

### The Rubric

```
Score each dimension 0–10:
1. Code correctness       — bugs, races, missing error handling, security holes,
                            money math precision (float vs decimal), idempotency gaps,
                            double-spend vectors, incorrect status code semantics
2. Codebase structure     — layering, separation, package cohesion, god-files,
                            domain boundary violations, AI/ML pipeline isolation
3. Performance            — N+1 queries, missing context cancellation, blocking I/O,
                            leaks, inference latency bottlenecks, unbounded batch sizes,
                            synchronous calls in async contexts
4. Testing                — unit/integration coverage, what's NOT tested,
                            missing financial edge case tests, no property-based tests
                            on money calculations, untested AI model output paths
5. Production readiness   — logging (no PII), metrics, tracing, secrets management,
                            graceful shutdown, retries with backoff, rate limiting,
                            compliance audit trail completeness, regulatory reporting
                            hooks, AML/KYC enforcement gaps
6. Senior / enterprise    — interface design, error taxonomy, ADRs, CI/CD pipeline,
                            dependency hygiene, fintech-specific: CBN/PCI-DSS/NDPR
                            compliance posture, AI model versioning, data residency,
                            multi-tenancy isolation
```

### Score Anchors

Anchors so scores reflect real production risk in a fintech context:

- **0–3**: Critical defects, unsafe to ship. In fintech this means financial
  exposure, regulatory violation risk, or user fund safety at risk.
- **4–5**: MVP-functional with known production risks. Acceptable for
  sandbox, not for live money movement.
- **6–7**: Solid. Passes mid-fintech peer review. CBN sandbox ready.
- **8–9**: Senior-quality, nits only. Series A due diligence ready.
- **10**: Best-in-class. Stripe / Paystack / Plaid standard.

### Language-Specific Audit Signals

Sub-agents must apply language-appropriate scrutiny alongside the universal
rubric:

**Go (microservices, transaction processing)**
- Goroutine leaks — launched without defined lifetime or context cancellation
- `log.Fatal` bypassing deferred cleanups
- Money amounts stored as `float64` instead of `int64` (minor units) or `decimal`
- Missing `TxPipeline` atomicity on multi-step Redis operations
- Bare `recover()` swallowing panics silently
- Missing `context.Context` propagation through call chains
- Race conditions on shared financial state (run audit with `-race` flag noted)

**TypeScript / Node.js (APIs, BFF, webhook handlers)**
- Floating point arithmetic on currency values — prescribe `dinero.js` or
  integer minor units
- Unhandled promise rejections in payment flows
- Missing request timeout on all outbound HTTP/gRPC calls
- `any` type suppressing type safety at financial data boundaries
- Synchronous operations blocking the event loop in high-throughput paths
- Missing input validation at API boundary before financial operations
- JWT secrets hardcoded or loaded without rotation support

**Python (AI/ML services, data pipelines, fraud models)**
- Unversioned model artifacts loaded at inference time
- No input validation schema before model inference — garbage in, confident
  garbage out
- Prediction logging missing model version, input hash, and latency
- Bare `except: pass` silencing inference failures
- Float precision in fee/interest calculations — prescribe `decimal.Decimal`
- Numpy/Pandas operations silently downcasting financial values
- Missing data drift detection on model inputs
- No confidence thresholding — all model outputs treated as equally certain

**Rust (high-performance settlement, cryptographic operations)**
- `unwrap()` / `expect()` in production paths — should be `?` propagation
- Unsafe blocks without documented invariants
- Missing bounds checks on financial array operations
- Integer overflow in transaction amount arithmetic without checked arithmetic

**C++ (core banking engines, low-latency trading)**
- Raw `new`/`delete` instead of smart pointers
- Missing RAII on database connection handles
- Integer overflow in transaction amount arithmetic
- Thread safety violations on shared financial state
- Missing AddressSanitizer / UBSan coverage noted in CI audit

### Fintech Domain-Specific Audit Signals

Beyond language, sub-agents must check these fintech-specific concerns:

**Money & Arithmetic**
- Currency amounts stored or calculated in floating point anywhere in the stack
- Rounding behavior undocumented or inconsistent across services
- No currency-aware arithmetic library in use
- Tax calculations using percentage multiplication on floats

**Payments & Transactions**
- Missing idempotency keys on all mutating payment endpoints
- No double-spend protection on wallet debit operations
- Transaction rollback paths not tested
- Settlement reconciliation gaps — debits and credits not balanced in audit log
- Webhook delivery not idempotent — side effects fire on every redelivery

**KYC / AML**
- KYC tier enforcement missing at transaction initiation — only checked at
  account creation
- Transaction monitoring rules not enforced server-side
- Sanctions screening absent or only checked at onboarding
- AML audit trail missing fields required for regulatory reporting
- Daily/weekly transaction limits not enforced server-side before processing

**Security**
- JWT validation not checking `aud`, `iss`, and `exp` — all three required,
  not just signature
- Session revocation not propagated across services — token valid after logout
- PII (email, BVN, NIN, phone number, OTP) appearing in log output
- API keys / secrets in source code or committed `.env` files
- SQL queries built via string concatenation — injection vector
- Path traversal via unsanitized file upload paths

**AI/ML in Production**
- Model version not logged alongside every inference
- No confidence threshold — model outputs all treated as equally certain
- Feature drift not monitored in production
- Shadow mode or canary deployment absent before full rollout
- Training/serving skew undetected
- No fallback behavior when model is unavailable or returns low-confidence output
- Inference endpoint not rate-limited

### Sub-Agent Output Format

Each sub-agent returns under ~800 words:

- Scores table (all six dimensions)
- 3 strengths (what is genuinely well-built)
- 5–10 issues ranked by severity, each with `file:line` + one-sentence
  description of the finding
- 3–5 quick wins (high impact, low effort)
- Language-specific findings (from the signal lists above)
- Fintech domain findings (from the domain signal list above)

Cap each report under ~800 words so the main context does not blow up.

### AUDIT.md Synthesis

After all sub-agents return, synthesize into a single `AUDIT.md` at the
repo root with:

- TL;DR table comparing all services across all six dimensions
- Fintech risk summary — findings that create financial exposure or
  regulatory risk, called out separately regardless of which service
- Cross-cutting themes — themes appearing in 3+ services, flagged for
  platform-layer remediation
- Per-service sections: strengths, top issues with `file:line`, quick wins
- A sequenced remediation roadmap in the batch format described in Phase 2

Do not recommend more than ~10 fixes per service. Pick the ones with the
highest blast-radius reduction per hour of engineering work.

---

## Phase 2 — Remediation Roadmap (Prescriptions, Not Changes)

This skill produces a remediation roadmap. It does not implement the roadmap.

Every item in the roadmap is a prescription — a complete, actionable
description of what must be changed, where, how, and in what order — for
the engineering team to execute.

**This skill does not write, edit, create, or delete any code.**
Not a single line. Not a variable rename. Not a comment. Not a config value.
The roadmap is the deliverable. The engineering team is the executor.

### Sequencing Logic

Prescriptions are sequenced by blast-radius reduction per engineering hour:

1. **Critical correctness / security** — financial exposure, auth bypass,
   PII leaks, money math errors. Nothing ships until these are resolved.
2. **Graceful shutdown / lifecycle** — prevents mid-deploy data corruption
   and ensures subsequent fixes are not rolled back by pod kills.
3. **Cross-service contracts** — idempotency, session revocation, KYC
   enforcement. Must be prescribed end-to-end: producer side and consumer
   side in the same batch.
4. **Helper-then-sweep** — introduce a primitive (error mapper, rate limiter,
   currency arithmetic helper), prove it on 5 call sites, then sweep remaining.
   Prescribe the primitive first, then the sweep as a separate batch.
5. **Test coverage floor** — minimum viable test suite for financial invariants
   and AI model output validation.
6. **Observability / compliance** — logging hygiene, metrics, audit trail
   completeness, regulatory reporting hooks.
7. **Documentation / configuration** — env-example, README, ADRs.

### Prescription Format

Every identified fix is documented in full:

```
FIX-[N]: [Title]
===========================================================
Service:         [service name]
File:            [exact/path/to/file.ext]
Line(s):         [N–M]
Severity:        [Critical / High / Medium / Low]
Dimension:       [Which rubric dimension this addresses]
Language:        [Go / TypeScript / Python / Rust / C++]
Fintech Domain:  [Payments / KYC / AML / Security / AI-Inference / etc. — if applicable]

CURRENT BEHAVIOR:
[Exact description of what the code does now. The problematic pattern
quoted or described with precise line reference. Never vague.]

WHY THIS IS A PROBLEM:
[Specific consequence in a fintech production context. Not "this is bad
practice" but "this allows double-spend because..." or "this leaks the
user's BVN to pod logs because..." or "this means the AML rule is
enforced at account creation but bypassed at transaction time because..."]

PRESCRIBED FIX:
[Complete implementation guidance. The exact change that must be made,
the pattern to apply, the library to use, the data type to switch to,
the Redis key schema to adopt, the middleware to add. Specific enough
that a mid-level engineer can execute without ambiguity. Code examples
in this section are illustrative prescriptions — reference implementations
to guide the engineer, not changes being made to the codebase.]

VERIFICATION:
[How the engineering team confirms this fix is correct after implementing.
Specific test to write. Specific log line to check. Specific behavior to
observe. Specific build command to run.]

CROSS-SERVICE IMPACT:
[If this fix requires a corresponding change in another service, name it
explicitly — service name, file, what must change — so it is not
implemented on one side and forgotten on the other.]

BATCH:
[Which remediation batch this belongs to and why it is sequenced here]
```

### Batch Shape (Engineering Team Execution Guide)

The roadmap organizes prescriptions into batches the engineering team
executes sequentially. Each batch is described with:

- **Batch name and objective** — what is true when this batch is complete
- **Prescriptions in this batch** — ordered list of FIX-[N] references
- **Sequencing rationale** — why these fixes belong together
- **Build verification** — the exact command to run after this batch
- **Cross-service pairing** — which fixes on other services must land
  in the same deployment window

```
BATCH [N]: [Name]
=================
Objective: [What is true when this batch is complete — binary, testable]

Prescriptions:
  FIX-[N]: [Title] — [one-line rationale for this batch]
  FIX-[N]: [Title] — [one-line rationale]

Build Verification (engineering team runs after executing this batch):
  cd <service-dir> && go build ./... && go vet ./...        # Go
  npx tsc --noEmit && npx eslint .                          # TypeScript
  mypy . --strict && ruff check .                           # Python
  cargo build && cargo clippy                               # Rust

Test Verification (if test suite exists):
  go test ./... -count=1 -race                              # Go
  npx vitest run --coverage                                 # TypeScript
  pytest --cov -x                                           # Python

Cross-Service Pairs:
  [FIX-N in this service] pairs with [FIX-M in other service] —
  both must land before either is effective.

Notes for engineering team:
  [Anything the team needs to know before executing — pre-existing rot
  in the area, known test failures unrelated to this batch, migration
  requirements, deployment ordering constraints]
```

---

## Phase 3 — Reporting After Each Audit Phase

After every discovery phase and every roadmap batch description, produce
a structured report section:

| Field | Content |
|---|---|
| **Files audited** | Table: file → one-line summary of findings. Link `file_path:line` for every finding. |
| **Why it matters** | One paragraph on the user-visible or business-visible consequence. Concrete: "checkout leaks gRPC connection-refused messages to clients" not "error handling is suboptimal." |
| **Updated scoreboard** | Same rubric. Show before/after columns when re-auditing after prescribed fixes have been implemented. |
| **What is still open** | 4–5 bullets in ROI order. End with a question: "address X next, pivot to Y, or is this sufficient for current milestone?" |

Never claim more than the audit found. If correctness was audited but
testing was not deeply explored, the testing score carries a confidence
qualifier. If a sub-agent's finding cannot be verified at `file:line`,
it is marked **UNVERIFIED** and the engineering team is asked to confirm
before executing the prescription.

---

## Common Fintech Patterns — Prescription Templates

These come up repeatedly across AI-fintech codebases. Have the prescription
shape ready.

### Error Leaking to Clients (`err.Error()` in Response Body)

**What to look for**: `err.Error()` or raw exception messages in HTTP
response bodies, exposing DB host:port, internal service names, stack
traces, or model inference errors to API consumers.

**Prescription shape**: Build a single `failErr(c, log, message, err)`
helper next to the existing `fail`/`ok` envelope. Map gRPC codes to HTTP
status codes:
- InvalidArgument → 400, Unauthenticated → 401, PermissionDenied → 403,
  NotFound → 404, AlreadyExists/Aborted → 409, FailedPrecondition → 412,
  ResourceExhausted → 429, Internal/Unknown → 500, Unavailable → 503,
  DeadlineExceeded → 504, Canceled → 408

Pass `status.Message()` through for user-input codes (client should see
validation text). Use a generic message for server-side codes (never leak
DB host:port, stack traces, model weights paths, or internal errors).

**Verification**: Table-driven test covering every gRPC code against a real
HTTP handler via `httptest`. Catches every regression across call sites.

---

### No Graceful Shutdown (`log.Fatal(app.Listen(...))`)

**What to look for**: `log.Fatal` wrapping the main listener, no signal
handling, deferred cleanups never running on pod termination.

**Prescription shape**:
- Replace with `signal.NotifyContext(ctx, os.Interrupt, syscall.SIGTERM)`
- Goroutine for `app.Listen`; main goroutine `select`s on `ctx.Done()` or
  listen error
- On signal: `shutdownCtx, cancel := context.WithTimeout(15s)`, call
  `app.ShutdownWithContext`, then Kafka consumer, WebSocket hub, gRPC server
- `defer` cleanups only run if the function returns — `log.Fatal` skips them

---

### Rate-Limit / Counter Race (`INCR` then `SET ttl`)

**What to look for**: Two-step Redis operations where `INCR` and `EXPIRE`
are not atomic — another request can INCR between them, resetting the window.

**Prescription shape**: Replace with atomic `INCR` + `EXPIRE … NX` in a
Redis pipeline. Surface as `IncrementWithTTL` on the cache port so the
limiter, OTP attempt counter, and idempotency key counter all share one
primitive.

---

### Hardcoded Secrets in Source

**What to look for**: API keys, JWT secrets, database credentials, or
webhook signing secrets as string literals in source files or committed
`.env` files.

**Prescription shape**: Move to viper config + env binding + `.env.example`
entry. For values already in git history: rotate the credential at the
provider. Removing from source does not remove from history — `git
filter-repo` is the remediation for historical exposure, and the credential
must be rotated regardless.

---

### Kafka Handlers Re-executing Side Effects on Redelivery

**What to look for**: Payment processing, wallet credits, or notification
sends inside Kafka handlers with no idempotency check — redelivery fires
the side effect again.

**Prescription shape**: Add an `inbox` table keyed on `(topic, partition,
offset)` or `event_id` in the same DB the handler writes to. Inside the
handler's transaction: `INSERT INTO inbox (event_id) ON CONFLICT DO NOTHING
RETURNING true` — if `false`, already processed, skip. If `true`, do the
work. Do not commit the Kafka offset until the handler returns nil.

---

### Session-Level Revocation Gap

**What to look for**: Logout, password change, or OTP reset invalidates
the refresh token but not already-issued access tokens, which remain valid
until expiry.

**Prescription shape**: Single Redis key `revoked:session:<id>` with TTL
matching refresh-token lifetime. Auth-side: on token validation, check both
`revoked:token:<jti>` (per-token) and `revoked:session:<sessionID>`
(per-session). Reject if either is set. Logout / password change / OTP-reset
/ refresh-token-reuse-detected → write the session key. Document the Redis
key schema as the cross-service contract in code comments on both producer
and consumer sides.

---

### PII in Logs

**What to look for**: Emails, OTPs, BVN, NIN, phone numbers, password
hashes, JWT payloads, or card numbers in `logger.*` calls or print statements.

**Prescription shape**: Log `user_id` only. Never log emails, OTPs, password
hashes, JWT contents, or document numbers. For cross-service correlation
without `user_id`, hash the email with BLAKE2 or SHA-256 with a service-side
pepper — same email maps to the same opaque string in different logs without
exposing PII. Audit DB tables (Postgres `audit_log`) may store email under
privileged access — but never to stdout where pod logs go to a shared
collector.

---

### Float Arithmetic on Money

**What to look for**: `float64`, `float32`, `number` (JS), or `float`
(Python) used for currency amounts, fee calculations, interest rates, or
tax computations anywhere in the stack.

**Prescription shape**:
- **Go**: Store amounts as `int64` in minor units (kobo, cents). Use
  `github.com/shopspring/decimal` for display formatting.
- **TypeScript**: Store as integer minor units. Use `dinero.js` for
  arithmetic. Never use `number` for money.
- **Python**: Use `decimal.Decimal` with explicit `ROUND_HALF_EVEN` for
  all financial calculations. Never use `float`.
- **All languages**: Document the minor unit convention (1 NGN = 100 kobo)
  in the model definition, not just in comments.

---

### KYC Tier Enforcement Gap

**What to look for**: KYC tier checked at account creation or login but
not re-evaluated at the moment of transaction initiation, allowing a user
whose KYC status changed to exceed their tier limits.

**Prescription shape**: Enforce KYC tier limits at the payment/transfer
service layer, not just the account service. The transaction service must
call the KYC enforcement check with the current tier at the time of
transaction, not rely on a cached tier from the session token. Prescribe
a middleware or service method that gates every mutating transaction on
current KYC status fetched at request time.

---

### AI Inference Without Observability

**What to look for**: Model inference endpoints with no version logging,
no input/output logging (or logging PII), no latency metrics, and no
fallback when the model is unavailable.

**Prescription shape**:
- Log: `{request_id, model_version, input_hash, prediction, confidence,
  latency_ms}` — never the raw input if it contains PII
- Expose Prometheus histogram: `inference_latency_ms` with `model_version`
  label
- Define a confidence threshold below which the prediction is rejected
  and a fallback rule fires
- Add a circuit breaker so model unavailability degrades gracefully instead
  of cascading to the calling service

---

## Anti-Patterns to Avoid

- **Making any code change, ever.** This skill audits. It reads, analyzes,
  scores, and prescribes. It does not edit files, create files, delete files,
  or alter any code artifact in any language. If there is an impulse to
  "just fix this quickly" — that impulse is out of scope. Write the
  prescription. Let the engineering team execute it.

- **Auditing only the language, missing the fintech domain.** A Go service
  can be perfectly idiomatic and still have a double-spend vector. A
  TypeScript API can have clean types and still log BVN numbers. Language
  quality and fintech correctness are separate audit dimensions — both
  must be assessed.

- **Prescribing one side of a cross-service contract.** Session revocation
  that only covers the Gateway, KYC enforcement that only covers the API
  layer, idempotency that only covers the producer — these are incomplete
  prescriptions. Always trace the full contract and prescribe both sides
  in the same roadmap batch.

- **Rewriting all 200 sites in one prescription.** Prescribe: introduce the
  primitive, prove it on 5 sites, then sweep. The engineering team needs
  checkpoints — a prescription that touches 200 files with no intermediate
  build verification is an invitation for an undebuggable regression.

- **Trusting a sub-agent's `file:line` without verification.** Sub-agents
  are often off by tens of lines after recent edits, or pointing at code
  that no longer exists. Always re-read the cited location before writing
  a prescription. Mark unverified findings explicitly.

- **Adding env-flag knobs for insecure behavior.** Default to the secure
  behavior. The opt-out flag enables the insecure path with a loud log
  warning. Never make the secure behavior the opt-in.

- **Closing a finding on one side of a cross-service contract.** The
  Gateway can have the perfect session-revocation check — if UMS does not
  write the key, nothing fires. Always trace the full producer → consumer
  loop before declaring a finding resolved.

- **Marking findings resolved based on sub-agent re-audit alone.** After
  the engineering team executes a batch, re-read the affected `file:line`
  yourself before updating the audit score. Sub-agents can report a finding
  as closed based on a description of the fix rather than the actual code.

- **Reporting "all green" when only the build is green.** "Build clean" is
  not "tests pass." The prescription must specify test verification. If no
  tests exist, the prescription must include a test to write.

- **Scope creep into pre-existing rot.** If the test file is broken with
  `model.OTP` undefined and that is unrelated to the current batch, call it
  out explicitly in the report and leave it in the open findings. Do not
  let it contaminate the current batch's scope or severity assessment.

---

## Tools Used (Most Important First)

- **Agent / Explore** for parallel discovery. One sub-agent per service,
  all in the same tool-call message so they run concurrently. Cap output
  with "under 800 words" so the main context survives.
- **TodoWrite** to track multi-batch prescriptions. Move items to
  `in_progress` before describing; mark complete when the prescription
  is written and verified against `file:line`.
- **Bash** for `grep` to find patterns and verify `file:line` claims before
  prescribing. Also used to run read-only analysis: `go vet`, `mypy`,
  `ruff`, `cargo clippy` in analysis mode. Never used to write files or
  execute changes.
- **Read / Explore** for verifying every `file:line` citation before it
  appears in a prescription. This is mandatory, not optional.

---

## When NOT to Use This Skill

- When the user wants code changes made directly. This skill produces audit
  reports and prescriptions. Direct code changes require the
  `senior-aiml-engineering-assistant` skill.
- One-off bug fix where the user knows exactly what is broken and wants it
  fixed, not audited.
- Greenfield code with no production traffic — audit overhead exceeds value.
- When the user explicitly says "don't audit, just do X." Respect the scope.
- When the codebase has no fintech domain concerns — use the generic
  `ai-code-reviewer` skill instead.

---

## A Worked Example: Auditing a Cross-Service Session-Revocation Gap

Pattern used in a real 6-service Go + Node AI-fintech monorepo:

1. **Discovery (Phase 1, parallel)**: Audit agents flagged "refresh-token
   revocation does not invalidate already-issued access tokens" as a HIGH
   finding in both the API Gateway (Go) and User Management Service (Node).
   Same root cause, two sides of the contract. Both `file:line` citations
   verified before prescribing.

2. **Schema decision (in the audit report)**: Redis key
   `revoked:session:<sessionID>`, TTL = refresh-token lifetime (7 days).
   Documented as the cross-service contract in AUDIT.md.

3. **Gateway prescription (consumer side — FIX-14)**:
   - File: `gateway/internal/auth/validator.go:87`
   - Add `RevokeSession`/`IsSessionRevoked` to the cache port interface
   - Impl reads `revoked:session:*` from Redis
   - `ValidateAccessToken` checks both blocklists before returning claims
   - Verification: table-driven test covering revoked and non-revoked sessions
   - Cross-service pair: FIX-15 (UMS producer side) must land same window

4. **UMS prescription (producer side — FIX-15)**:
   - File: `ums/services/jwt.service.ts:234`
   - Mirror `revokeSession` and `revokeAllSessions` methods
   - Same Redis key schema as Gateway prescription
   - Logout, ChangePassword, ResetPassword, refresh-reuse-detection all
     write the session key
   - Verification: integration test confirming Gateway rejects token after
     UMS logout

5. **Reuse detection prescription (FIX-16)**:
   - File: `ums/services/jwt.service.ts:198`
   - Return `ErrRefreshTokenReused` when JTI is structurally valid but
     blocklisted
   - Handler escalates to `revokeSession` + RiskHigh security event

6. **Audit report update**: 8 files affected across 2 services, schema
   diagram (UMS writes → Redis key → Gateway reads), confirmation that
   both services must share the same Redis cluster for the contract to
   fire, and a note that the finding is **PRESCRIBED but not RESOLVED**
   until the engineering team executes and re-audit confirms the fix.

That is the shape. Discovery → verified citations → cross-service contract
defined → prescriptions on both sides → engineering team executes →
re-audit confirms resolution. This skill owns everything before "engineering
team executes." Nothing after.
```

---