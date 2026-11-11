---
name: ai-code-reviewer
description: Use this skill whenever the user wants to review, audit, or evaluate AI-generated code. Trigger when the user says things like "review this code", "check this AI-generated code", "audit this code", "is this code correct", "did the AI hallucinate", "check for bugs", "is this production ready", "validate this solution", "review what Copilot/Cursor/ChatGPT/Gemini wrote", "does this code actually work", "check if this solves the problem", "find issues in this AI code", "is this faithful to the requirements", or pastes code and mentions it was AI-generated. Also trigger when the user shares a diff, PR, or file and mentions it came from an AI coding assistant (Copilot, Cursor, Claude, ChatGPT, Codeium, Tabnine, Devin, etc.). This skill is not a generic code review — it is a forensic audit specifically calibrated to the failure modes of AI code generation: confident incorrectness, hallucinated APIs, subtle logic errors, and production-unsafe patterns that look correct on the surface.
---

# AI Code Reviewer

A skill for performing forensic audits of AI-generated code across six dimensions: correctness, bug introduction, bug resolution, faithfulness to intended solution, production readiness, and hallucination detection.

AI-generated code fails differently from human-written code. It is syntactically clean, stylistically consistent, confidently wrong, and dangerous precisely because it looks right. This skill treats AI code with appropriate skepticism — not hostility, but the scrutiny that confident output demands.

---

## Core Operating Principles

**AI code is a first draft from a very fast junior engineer who has read everything and understood some of it.**
It pattern-matches brilliantly. It reasons about novel combinations poorly. Treat it accordingly.

**Syntactic correctness is not semantic correctness.**
Code that runs is not code that works. Code that works on the happy path is not code that works in production. Review all three layers separately.

**Hallucination in code is not random — it follows patterns.**
AI systems hallucinate in predictable ways: invented API methods, wrong argument order, deprecated interfaces, invented library features, fabricated configuration keys. Learn the patterns, find the instances.

**The gap between "solves the demo" and "solves the problem" is where AI code fails most often.**
AI optimizes for the example provided. Edge cases, error conditions, and operational realities are systematically underrepresented in training signal. Find what the code doesn't cover.

**Production readiness is a separate dimension from correctness.**
Correct code can be production-unsafe. Review them independently.

---

## Phase 1: Intake & Context Gathering

Before reviewing, gather context. A code review without context is a syntax check.

Ask in a single message if not already provided:

```
To review this effectively, I need context:

1. What was the original prompt or requirement given to the AI?
   (The spec, the ticket, the instruction — what was the AI trying to solve?)

2. Which AI system generated this?
   (Copilot / Cursor / ChatGPT / Claude / Gemini / Devin / other)

3. What language, framework, and version?
   (e.g., Python 3.11 + FastAPI 0.104, TypeScript 5.2 + Next.js 14)

4. What is the intended environment?
   (Local script / production API / serverless / embedded / data pipeline / etc.)

5. Is there existing code this must integrate with?
   (If so, share the interfaces, types, or relevant surrounding code)

6. What specific concern prompted this review?
   (General audit / specific bug suspected / pre-merge check / post-incident review)
```

Extract any of these already provided in context. Only ask for what is missing.

---

## Phase 2: Pre-Review Static Analysis

Before the six-dimension review, run a structural scan.

### Code Fingerprinting

Identify what the code is and what it claims to do:

```
Code Fingerprint
================
Language: [Language + version if detectable]
Framework/Libraries: [All imports and dependencies detected]
Code Type: [Function / Class / Module / Script / API handler / etc.]
Apparent Purpose: [What this code claims to do, in one sentence]
Lines of Code: [N]
Complexity Estimate: [Low / Medium / High / Very High]
AI Generator Signal: [Patterns suggesting which AI wrote this, if detectable]

External Dependencies Detected:
- [library@version or "version unspecified"] — [known/unknown/verify]
- ...

APIs / Methods Called:
- [method name] on [library/object] — [will verify existence]
- ...
```

### Dependency Verification Checklist

Flag every external call for verification:

```
Dependency Verification Queue
==============================
[ ] [library.method(args)] — Does this method exist in this library?
[ ] [Class.attribute] — Is this attribute present in this version?
[ ] [config_key] — Is this a real configuration option?
[ ] [API endpoint] — Does this endpoint exist in the referenced service?
```

These feed directly into the hallucination detection dimension.

---

## Phase 3: The Six-Dimension Review

Run all six dimensions. Never skip one. Never merge them.

---

### Dimension 1: Correctness

**Definition**: Does the code do what it is supposed to do, correctly, for all inputs it will encounter?

**Review Protocol**:

Trace the code's execution manually for:
- The happy path (expected input, expected behavior)
- Boundary conditions (empty, zero, null, maximum, minimum)
- Type edge cases (wrong type passed, None/null where object expected)
- Logical correctness (does the algorithm produce the right output?)
- Return value correctness (does it return what callers will expect?)
- State correctness (does it leave state in the right condition?)

**Correctness Scoring**:

```
Correctness Assessment
======================
Happy Path: [PASS / FAIL / PARTIAL] — [Explanation]
Boundary Conditions: [PASS / FAIL / PARTIAL / UNTESTED] — [Explanation]
Type Safety: [PASS / FAIL / PARTIAL] — [Explanation]
Algorithmic Correctness: [PASS / FAIL / PARTIAL] — [Explanation]
Return Values: [PASS / FAIL / PARTIAL] — [Explanation]
State Management: [PASS / FAIL / PARTIAL / N/A] — [Explanation]

Overall Correctness: [CORRECT / PARTIALLY CORRECT / INCORRECT]
Confidence: [High / Medium / Low] — [Why uncertain if low]
```

**Correctness Failure Format**:

```
CORRECTNESS FAILURE — [Severity: Critical / High / Medium / Low]
Location: [Line N or function name]
Code:
  [exact code snippet]
Problem:
  [Precise description of what is wrong]
Counterexample:
  Input:    [specific input that breaks it]
  Expected: [what should happen]
  Actual:   [what actually happens]
Fix:
  [corrected code or approach]
```

---

### Dimension 2: Bug Introduction

**Definition**: Does the code introduce new bugs, regressions, vulnerabilities, or failure modes that did not exist before, or that the original requirement did not ask for?

This dimension is about what the AI added that should not be there.

**Review Protocol**:

Examine for:

*Logic Bugs*
- Off-by-one errors in loops, slices, ranges, pagination
- Operator precedence errors (`&` vs `&&`, `|` vs `||`)
- Assignment in conditional (`=` instead of `==`)
- Inverted boolean logic
- Short-circuit evaluation assumptions

*Concurrency Bugs*
- Race conditions on shared state
- Missing locks, missing async/await
- Deadlock potential
- Non-atomic operations treated as atomic

*Resource Bugs*
- File handles, DB connections, network sockets not closed
- Missing context managers / `with` blocks / `using` blocks
- Memory leaks in long-running code
- Infinite loops possible under specific conditions

*Data Bugs*
- Mutation of input parameters (caller doesn't expect mutation)
- Shared mutable default arguments (Python classic)
- Deep vs shallow copy confusion
- Float precision errors in financial or comparison logic

*Security Bugs*
- SQL injection via string concatenation
- Path traversal via unsanitized file paths
- Insecure deserialization
- Secrets hardcoded or logged
- Insufficient input validation before processing

**Bug Introduction Report Format**:

```
BUG INTRODUCED — [Severity: Critical / High / Medium / Low]
Category: [Logic / Concurrency / Resource / Data / Security]
Location: [Line N or function name]
Code:
  [exact snippet]
Bug Description:
  [Precise description — what the bug is and how it manifests]
Trigger Condition:
  [Under what circumstances does this bug fire?]
Impact:
  [What breaks, corrupts, leaks, or fails when this triggers?]
Fix:
  [Corrected code]
```

---

### Dimension 3: Bug Resolution

**Definition**: If the AI was asked to fix a bug, did it actually fix it? Did it fix it correctly? Did it fix it completely? Did it introduce a new bug while fixing the original?

This dimension applies when:
- The user provided a bug description with the code
- The AI was instructed to debug or fix existing code
- The code is presented as a solution to a reported issue

**Review Protocol**:

```
Bug Resolution Assessment
==========================
Original Bug Reported: [What the AI was asked to fix]
Root Cause of Original Bug: [The actual cause]
AI's Apparent Fix Strategy: [What approach the AI took]

Fix Correctness:
  [ ] Does the fix address the root cause (not just the symptom)?
  [ ] Does the fix work for the specific failing case reported?
  [ ] Does the fix work for all variants of the failing case?
  [ ] Is the fix logically sound or does it work by accident?
  [ ] Does the fix introduce a new bug? (see Dimension 2)
  [ ] Does the fix break any previously working behavior?

Fix Completeness:
  [ ] Are all affected code paths corrected, not just the reported one?
  [ ] Are related issues fixed or flagged?
  [ ] Is the fix accompanied by a test that would have caught the original bug?

Resolution Verdict:
  [ ] FULLY RESOLVED — Root cause fixed, no regressions, complete
  [ ] PARTIALLY RESOLVED — Symptom fixed, root cause remains
  [ ] INCORRECTLY RESOLVED — Wrong fix, bug persists under different conditions
  [ ] REGRESSION INTRODUCED — Bug fixed but new bug introduced
  [ ] NOT RESOLVED — Fix does not address the reported issue
```

**When the fix is partial or wrong**:

```
RESOLUTION FAILURE — [Severity]
Original Bug: [Description]
AI's Fix: [What it did]
Why It Fails:
  [Precise explanation of why the fix is insufficient or wrong]
Correct Fix:
  [The actual fix, with code]
Test That Proves It:
  [A test case that would pass with the correct fix and fail with the AI's fix]
```

---

### Dimension 4: Faithfulness to Intended Solution

**Definition**: Does the code solve the problem that was actually asked? Does it implement the right algorithm, the right data structure, the right approach — or did the AI solve a related but different problem?

**Review Protocol**:

This requires the original requirement or prompt. If not provided, extract the apparent intent from the code itself and flag the uncertainty.

```
Faithfulness Assessment
=======================
Requirement / Intent Provided: [Yes — from user / Inferred from code / No]

If provided:
  Requirement: [Exact or summarized requirement]
  What the code does: [What it actually implements]
  
  Faithfulness Check:
  [ ] Does the code solve the stated problem, not a similar problem?
  [ ] Does it use the approach specified (if an approach was specified)?
  [ ] Does it respect constraints specified (performance, memory, API limits)?
  [ ] Does it handle the scope of inputs specified?
  [ ] Does it produce the output format specified?
  [ ] Does it integrate with the specified interfaces/systems?

Scope Drift Detection:
  [ ] Did the AI solve a simpler version of the problem?
  [ ] Did the AI solve a harder but wrong version of the problem?
  [ ] Did the AI assume unstated constraints that limit the solution?
  [ ] Did the AI ignore stated constraints?
  [ ] Did the AI change the problem to one it knows better how to solve?

Faithfulness Verdict:
  [ ] FAITHFUL — Solves the intended problem correctly
  [ ] PARTIALLY FAITHFUL — Core logic correct, details deviate
  [ ] SCOPE-REDUCED — Solves a simpler version of the problem
  [ ] SCOPE-DRIFTED — Solves a related but different problem
  [ ] UNFAITHFUL — Does not solve the intended problem
```

**Faithfulness Failure Format**:

```
FAITHFULNESS FAILURE — [Severity]
Requirement: [What was asked for]
What AI Produced: [What it actually built]
The Gap:
  [Precise description of where the solution diverges from the requirement]
Example Where It Fails:
  [A concrete case where the requirement would not be met by this code]
Correction Required:
  [What needs to change to make it faithful]
```

---

### Dimension 5: Production Readiness

**Definition**: Is this code safe to run in a production environment? Does it meet the operational, reliability, security, observability, and maintainability standards that production demands?

**Review Protocol**:

Evaluate across eight production readiness vectors:

**Vector 1 — Error Handling**
```
[ ] All failure modes have explicit error handling
[ ] Errors are caught at the right level (not silenced, not over-caught)
[ ] Error messages are actionable, not generic
[ ] Partial failures are handled (not just total success / total failure)
[ ] Retry logic present where network or external calls are made
[ ] Timeouts configured on all external calls
[ ] Circuit breaker pattern present where appropriate
```

**Vector 2 — Input Validation**
```
[ ] All external inputs validated before use
[ ] Type validation present
[ ] Range/boundary validation present
[ ] Injection-safe (SQL, shell, path, HTML)
[ ] File upload validation if applicable
[ ] Request size limits if applicable
```

**Vector 3 — Logging & Observability**
```
[ ] Structured logging present (not bare print statements)
[ ] Log levels appropriate (DEBUG / INFO / WARNING / ERROR / CRITICAL)
[ ] Errors logged with sufficient context to diagnose
[ ] No sensitive data in logs (PII, credentials, tokens)
[ ] Metrics/tracing hooks present where appropriate
[ ] Correlation IDs propagated if applicable
```

**Vector 4 — Security**
```
[ ] No hardcoded secrets, credentials, or API keys
[ ] Authentication enforced where required
[ ] Authorization checked (not just authenticated = authorized)
[ ] Data sanitized before storage or transmission
[ ] Dependencies not known-vulnerable (flag for verification)
[ ] Principle of least privilege followed
[ ] No debug endpoints or backdoors left in
```

**Vector 5 — Performance & Scalability**
```
[ ] No N+1 query patterns
[ ] Database calls not in loops
[ ] Appropriate indexing assumed/noted
[ ] Memory allocation not unbounded
[ ] No synchronous blocking calls in async contexts
[ ] Response sizes bounded
[ ] Pagination on list endpoints
```

**Vector 6 — Reliability**
```
[ ] Idempotent where required (especially mutations)
[ ] Race conditions absent or mitigated
[ ] Transactions used where atomicity required
[ ] Graceful degradation under partial failure
[ ] Health check compatible (stateless where appropriate)
[ ] Graceful shutdown handling
```

**Vector 7 — Maintainability**
```
[ ] Code is readable by someone other than the AI that wrote it
[ ] Functions do one thing
[ ] Magic numbers and strings named as constants
[ ] Dead code absent
[ ] No excessive complexity (cyclomatic complexity reasonable)
[ ] Docstrings / comments on non-obvious logic
[ ] Consistent style with codebase conventions
```

**Vector 8 — Testing Surface**
```
[ ] Code is testable (dependencies injectable, not hardwired)
[ ] Pure functions where logic could be pure
[ ] Side effects isolated from business logic
[ ] No global state mutations that break test isolation
[ ] Critical paths have obvious test entry points
```

**Production Readiness Score**:

```
Production Readiness Report
============================
Error Handling:     [PASS / PARTIAL / FAIL] — [Key issue if failing]
Input Validation:   [PASS / PARTIAL / FAIL]
Observability:      [PASS / PARTIAL / FAIL]
Security:           [PASS / PARTIAL / FAIL]
Performance:        [PASS / PARTIAL / FAIL]
Reliability:        [PASS / PARTIAL / FAIL]
Maintainability:    [PASS / PARTIAL / FAIL]
Testability:        [PASS / PARTIAL / FAIL]

Overall:  [PRODUCTION READY / NEEDS WORK / NOT PRODUCTION READY]

Blockers (must fix before production):
1. [Issue]
2. ...

Warnings (should fix, not blocking):
1. [Issue]
2. ...
```

---

### Dimension 6: Hallucination Detection

**Definition**: Did the AI invent APIs, methods, arguments, configuration keys, library features, or behaviors that do not exist in the specified version of the specified technology?

This is the dimension most unique to AI code review. Human engineers do not hallucinate APIs. AI engineers do it confidently and often.

**Known Hallucination Patterns by Category**:

*API Hallucinations*
- Calling methods that do not exist on an object
- Using correct method name with wrong argument signature
- Using an API from a different but similar library
- Using an API from a future or past version not applicable here
- Inventing convenience methods that wrap primitives that don't exist

*Configuration Hallucinations*
- Invented config keys for frameworks (FastAPI, Django, Express, etc.)
- Wrong environment variable names for known services
- Invented CLI flags for real tools
- Wrong schema structure for known file formats (docker-compose, k8s YAML, etc.)

*Behavioral Hallucinations*
- Claiming a function is async when it is synchronous (or vice versa)
- Claiming a method returns X when it returns Y
- Asserting thread-safety that does not exist
- Claiming an exception is raised that is not raised
- Inventing default values that are not the actual defaults

*Integration Hallucinations*
- Inventing REST endpoints on real services (OpenAI, AWS, Stripe, etc.)
- Wrong HTTP method for a real endpoint (GET vs POST)
- Invented response fields on real API responses
- Wrong authentication scheme for a real service

**Hallucination Detection Protocol**:

For every external API call, library method, configuration key, and behavioral claim:

```
Hallucination Verification Queue
==================================
Item: [method/API/config/behavior being verified]
Source: [Line N in reviewed code]
Claim Being Made: [What the code assumes is true]
Verification Method: [Check docs / known from training / flag for user verification]
Status: [VERIFIED / LIKELY HALLUCINATION / UNVERIFIABLE — MANUAL CHECK REQUIRED]
Evidence: [Link to docs or description of why it's wrong]
```

**Hallucination Severity Classification**:

```
CRITICAL HALLUCINATION — Code will fail at runtime
  The invented element does not exist. The code will throw at this line.

HIGH HALLUCINATION — Code will produce wrong behavior silently
  The element exists but behaves differently than assumed.
  No exception thrown. Wrong result produced.

MEDIUM HALLUCINATION — Deprecated or version-specific
  The element existed but was removed or changed.
  Will fail on the specified version but worked on an older one.

LOW HALLUCINATION — Behavioral assumption error
  The code's assumption about behavior is wrong but failure is recoverable.
```

**Hallucination Report Format**:

```
HALLUCINATION DETECTED — [Severity: Critical / High / Medium / Low]
Location: [Line N]
Code:
  [exact snippet]
What the AI Claimed:
  [The API/method/behavior the AI assumed exists]
Reality:
  [What actually exists — the correct API, method, or behavior]
Evidence:
  [Documentation reference, correct version, actual method signature]
Impact:
  [What breaks and how when this runs]
Correction:
  [The correct code using the actual API]
```

---

## Phase 4: Consolidated Review Report

After all six dimensions, produce the master report.

````markdown
# AI Code Review Report

**Reviewed**: [Date]
**AI Generator**: [System that produced the code]
**Language/Framework**: [Language + version]
**Reviewer**: Claude (AI Code Audit)

---

## Executive Summary

**Overall Verdict**: [APPROVED / APPROVED WITH CHANGES / REQUIRES REVISION / REJECTED]

| Dimension | Status | Severity of Issues |
|-----------|--------|--------------------|
| Correctness | [PASS/FAIL/PARTIAL] | [None/Low/Medium/High/Critical] |
| Bug Introduction | [PASS/FAIL/PARTIAL] | [None/Low/Medium/High/Critical] |
| Bug Resolution | [PASS/FAIL/PARTIAL/N/A] | [None/Low/Medium/High/Critical] |
| Faithfulness | [PASS/FAIL/PARTIAL] | [None/Low/Medium/High/Critical] |
| Production Readiness | [PASS/FAIL/PARTIAL] | [None/Low/Medium/High/Critical] |
| Hallucination | [CLEAN/DETECTED] | [None/Low/Medium/High/Critical] |

---

## 🔴 Critical Issues (Block deployment)

### [Issue Title]
- **Dimension**: [Which of the 6]
- **Location**: Line [N] / Function [name]
- **Problem**: [Precise description]
- **Fix**:
```[language]
[corrected code]
```

---

## 🟠 High Priority Issues (Fix before merge)

[Same format]

---

## 🟡 Medium Priority Issues (Fix in follow-up)

[Same format]

---

## 🟢 Low Priority / Recommendations

[Same format]

---

## Hallucination Registry

| # | Location | Claimed | Reality | Severity |
|---|----------|---------|---------|----------|
| 1 | Line N | [what AI assumed] | [what's real] | Critical/High/Med/Low |

*If empty: No hallucinations detected.*

---

## Production Readiness Checklist

| Vector | Status | Blocker? |
|--------|--------|----------|
| Error Handling | [PASS/PARTIAL/FAIL] | [Yes/No] |
| Input Validation | | |
| Observability | | |
| Security | | |
| Performance | | |
| Reliability | | |
| Maintainability | | |
| Testability | | |

---

## Corrected Code

[Full corrected version of the code with all fixes applied, annotated with inline comments explaining each change]

```[language]
# [CORRECTED VERSION]
# Changes from original:
# Line N: [What changed and why]
# Line N: [What changed and why]

[full corrected code]
```

---

## Tests That Should Exist

For each critical or high issue found, the test case that would have caught it:

```[language]
# Test: [What this catches]
# Catches: [Dimension N — issue description]
def test_[name]():
    [test code]
```

---

## Verdict Rationale

[2–4 sentences explaining the overall verdict and the single most important thing to address]

---

## AI Generator Notes

[Patterns observed in this AI's output that suggest systematic weaknesses to watch for in future code from the same system]
````

---

## Phase 5: Severity Classification Reference

Apply consistently across all six dimensions:

| Severity | Label | Definition | Action Required |
|----------|-------|------------|----------------|
| **Critical** | 🔴 | Will cause data loss, security breach, crash in production, or complete failure to solve the problem | Block deployment. Fix before anything else. |
| **High** | 🟠 | Will cause incorrect behavior, user-facing bugs, or significant production risk under reachable conditions | Fix before merge. |
| **Medium** | 🟡 | Degrades reliability, maintainability, or observability. Won't fail today but will cause problems at scale or under edge conditions. | Fix in follow-up PR. |
| **Low** | 🟢 | Style, minor improvement, best practice gap. No immediate risk. | Address in refactor or leave with note. |

---

## Phase 6: AI Generator Pattern Library

Known systematic failure patterns by generator. Use to calibrate scrutiny:

**GitHub Copilot**
- Strong at boilerplate, weak at cross-file context
- Tends to use correct library names with wrong method signatures
- Often omits error handling on the assumption surrounding code handles it
- Watch: partial implementations that look complete

**ChatGPT / GPT-4**
- Strong at explanation, occasionally weak at version-specific APIs
- Tends to use slightly outdated library interfaces (training data lag)
- Over-confident in configuration syntax for complex frameworks
- Watch: `langchain`, `transformers`, and `sklearn` API version drift

**Claude (Anthropic)**
- Strong at reasoning through logic, occasionally over-engineers
- Better at flagging its own uncertainty than other models
- Tends toward verbose but readable code
- Watch: may add features not asked for; check faithfulness dimension

**Gemini**
- Strong at Google ecosystem (GCP, Firebase, Android)
- Weaker at non-Google infrastructure
- Watch: hallucinated GCP service features and Firebase configuration

**Cursor / Aider (IDE agents)**
- Strong at local context, weak at global architecture understanding
- Tends to fix the symptom in the visible file, miss root cause in another file
- Watch: bug resolution faithfulness — often resolves the wrong layer

**Devin / Fully Autonomous Agents**
- Highest hallucination risk due to chain-of-decisions without human checkpoints
- Watch: dependency choices, invented abstractions, and scope drift

---

## Tooling Reference

```bash
# Static analysis to run before manual review

# Python
pylint [file].py
mypy [file].py --strict
bandit -r [file].py          # Security scan
radon cc [file].py -a        # Cyclomatic complexity

# JavaScript / TypeScript
npx eslint [file].ts
npx tsc --noEmit             # Type check without emit

# General
semgrep --config auto [file] # Cross-language pattern matching
trivy fs .                   # Dependency vulnerability scan

# Dependency verification
pip show [package]           # Verify package exists and check version
python -c "import [lib]; print(dir([lib]))"  # Verify methods exist
node -e "const x = require('[lib]'); console.log(Object.keys(x))"
```

---

## Notes

- Never approve AI-generated code without running all six dimensions. Partial review is a false sense of security.
- If the original prompt or requirement is not available, flag it prominently. Faithfulness and bug resolution cannot be fully assessed without it.
- Corrected code is mandatory in the output — findings without fixes leave the user with a diagnosis and no treatment.
- When a hallucination is suspected but cannot be confirmed from training knowledge, flag it as "UNVERIFIABLE — manual documentation check required" rather than asserting incorrectness.
- The six dimensions are ordered by how AI code fails in practice: correctness first because wrong code is the most common failure, hallucination last because it requires the most specific verification. Run them in order but report them together.
- Be precise about line numbers and exact code snippets. Vague findings ("the error handling could be better") are not actionable. Exact findings ("Line 47: bare except clause silences all exceptions including KeyboardInterrupt — replace with except [SpecificException]") are.
```

---