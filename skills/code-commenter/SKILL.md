---
name: code-commenter
description: Use this skill whenever the user wants to add comments, docstrings, or inline documentation to code. Trigger when the user says things like "add comments to this code", "document this function", "explain this code with comments", "add docstrings", "comment this", "make this code self-explanatory", "add inline comments", "document why this works the way it does", "help a new developer understand this", "annotate this code", or pastes code and asks for it to be documented or explained through comments. Also trigger when the user shares a file, module, class, or function and wants the reasoning, decisions, and intent made visible through human-voice commentary. This skill does not rewrite code. It illuminates it. Every comment written sounds like a thoughtful senior engineer speaking to the next developer who will read this — explaining not just what the code does, but why it does it that way, what was considered and rejected, and what the next person must not break.
---

# Code Commenter

A skill for adding human-voice comments and docstrings to code. The goal is not annotation — it is communication. Every comment written by this skill sounds like a senior engineer leaving a note for the next developer: explaining decisions, surfacing constraints, flagging traps, and making the invisible reasoning behind the code permanently visible.

**This skill has one job: illuminate code. It does not touch code.**
It never alters logic, renames variables, restructures functions, refactors
classes, or changes anything that affects execution. The code that goes in
is byte-for-byte identical to the code that comes out — only comments and
docstrings are added. If a software engineering concern is discovered during
commenting, it is flagged in writing. It is never silently fixed.
---

## Core Philosophy

**Comments explain the why. Code explains the what.**
If a comment restates what the code already says, it is noise. If a comment explains why the code is the way it is — why this approach over the obvious alternative, why this order, why this guard — it is signal. Write signal only.

**Write as a human communicating thoughts.**
Not documentation syntax. Not technical annotations. A real person, with judgment and experience, speaking directly to the next developer. Use first person where it adds clarity. Use "we" when it reflects a team decision. Name the constraint. Name the tradeoff. Name the thing the reader must not break.

**Inline comments belong at divergence points.**
Not every line needs a comment. The lines that need comments are:
- Where the code does something non-obvious
- Where an architectural or design decision was made
- Where a simpler approach was deliberately rejected
- Where a future developer will be tempted to "fix" something that is correct
- Where an external constraint (API behavior, regulatory rule, legacy system) forces a specific implementation
- Where a guard or early return exists for a non-obvious reason
- Where ordering matters and changing it would break something subtle

**Docstrings explain why the function exists, not what its signature says.**
The signature already tells you the parameters and return type. The docstring tells you why this function was created, what problem it solves, what invariants it maintains, what the caller must understand before using it, and what the implementer must not violate when changing it.

---

## Phase 1: Code Intake & Analysis

Before writing a single comment, read the code fully and build a complete understanding.

### Step 1.1 — Structural Analysis

Map what exists:

```
Code Analysis
=============
Language:          [Python / TypeScript / Go / Rust / C++ / etc.]
Code Unit:         [Function / Method / Class / Module / File]
Lines of Code:     [N]
External Calls:    [APIs, libraries, services called]
Decision Points:   [if/else branches, type switches, early returns]
Critical Sections: [error handling, transactions, state mutations]
Non-obvious Logic: [algorithms, transformations, ordering dependencies]
```

### Step 1.2 — Identify What Needs Commentary

Scan for these comment-worthy patterns before writing anything:

```
Comment Trigger Inventory
==========================

ARCHITECTURAL DECISIONS
[ ] Design patterns applied (and why this pattern here)
[ ] Approaches deliberately rejected
[ ] Constraints that forced this design
[ ] Public API / interface decisions

DIVERGENCE POINTS
[ ] Type branching (isinstance, type switches, discriminated unions)
[ ] Environment-specific paths
[ ] Feature flag branches
[ ] Permission/role branches

CRITICAL LOGIC
[ ] Business rule implementations
[ ] Financial calculations
[ ] Compliance/regulatory enforcement
[ ] Security-sensitive operations

NON-OBVIOUS BEHAVIOR
[ ] Ordering that is load-bearing (change order = break behavior)
[ ] Mutation of input parameters (caller must know)
[ ] Side effects that are not obvious from the function name
[ ] Implicit assumptions about input state

GUARD CLAUSES & EARLY RETURNS
[ ] Why this input is invalid
[ ] What the caller must have guaranteed before reaching here
[ ] Why we return early instead of processing

ERROR HANDLING
[ ] Why this error is re-raised vs swallowed
[ ] What the upstream caller depends on from this exception
[ ] Why we log both message and type (or just one)

EXTERNAL CONSTRAINTS
[ ] Third-party API quirks that forced unusual code
[ ] Legacy system behaviors being worked around
[ ] Regulatory requirements encoded in logic

FUTURE GUIDANCE
[ ] Things future developers will be tempted to "fix" that must not change
[ ] Extension points and how to use them correctly
[ ] Known limitations and their workarounds
[ ] What belongs in this code vs what must stay out of it
```

### Step 1.3 — Understand Before Writing

For each identified comment trigger, answer:
1. **What decision was made here?**
2. **Why was this decision made over the obvious alternative?**
3. **What constraint, requirement, or tradeoff drove this?**
4. **What would break if someone changed this?**
5. **What does the next developer need to know that the code cannot tell them?**

Do not write comments until these questions have answers. Commenting without understanding produces noise.

**Hard boundary**: Understanding the code is the limit of this skill's
authority. If analysis reveals a bug, a logic error, a naming issue, or a
design concern, that finding is recorded in the Developer Review Flags
section of the output. Under no circumstances is it corrected inline.
This skill is not an engineering role. It does not hold one and must not act like one.

---

## Phase 2: Docstring Standards

### The Docstring Philosophy

A docstring is not a repeat of the function signature in prose. It is the answer to: **"Why does this function exist, and what does a caller or implementer need to know that the code itself cannot tell them?"**

### Docstring Structure

```python
def function_name(params) -> ReturnType:
    """
    [One-line summary — what this function IS, not what it DOES mechanically]

    Why this exists:
    ----------------
    [The problem this function solves. What was happening before it existed,
    or what would happen if you removed it. This is the most important part.
    If you cannot write this, you do not understand the function well enough
    to comment it.]

    [Additional context paragraphs if needed. Each paragraph covers one
    distinct concern: design decisions, invariants, constraints, behaviors
    that are non-obvious from the signature.]

    Args:
        param_name: [Not the type — the type is in the signature. What this
                     value represents, what it must contain, what it must NOT
                     contain, and any non-obvious constraints.]

    Returns:
        [Not the type — what the value represents. What the caller can do
         with it. Whether it can be None/null and what that means.]

    Raises:
        ExceptionType: [When and why this is raised. What the caller should
                        do when they catch it.]

    Note:
        [Anything that doesn't fit above but must be communicated:
         - Threading / concurrency behavior
         - Idempotency guarantees (or lack thereof)
         - Performance characteristics the caller must account for
         - Side effects not obvious from the name
         - Things this function deliberately does NOT handle]
    """
```

### Docstring Examples by Pattern

**Router / Dispatcher pattern:**
```python
"""
Entry point for [N types of X] that each require different workflows.

Why this exists:
----------------
[Type A] and [Type B] share a common trigger but diverge fundamentally in
how they must be processed. Rather than forcing callers to know which
internal method to call, this function inspects the type and routes to the
correct workflow, keeping the public interface stable as processing logic
evolves.

Callers should not attempt to call the internal processing methods directly —
the routing logic here ensures each type receives the correct pre/post processing.
"""
```

**Transformation / Mapping pattern:**
```python
"""
Converts [SourceType] into the shape [TargetSystem] expects.

Why this exists:
----------------
[TargetSystem] was designed for [use case] and expects [specific structure].
Our internal model represents [different use case] with [different structure].
This function is the single translation point between our domain model and
the external contract — changes to either side should flow through here.

The mapping is not symmetric: [specific asymmetry — fields that exist on
one side but not the other, and why].
"""
```

**Validation / Guard pattern:**
```python
"""
Validates that [entity] is in a state where [operation] is safe to perform.

Why this exists:
----------------
[Operation] makes assumptions about [entity state] that, if violated, would
cause [specific bad outcome: data corruption / incorrect charges / partial
state / etc.]. Rather than embedding these checks at every call site,
centralising validation here ensures the rules are consistent and auditable.

This function raises rather than returns a boolean because a failed validation
means the caller has a programming error or received tampered input — both
conditions should be loud, not silently handled.
"""
```

**Error handler pattern:**
```python
"""
Handles [error condition] from [source] and [action taken].

Why this exists:
----------------
[Source] surfaces errors in [format/pattern] that are not immediately
usable by our orchestration layer. This handler normalizes them into
[our error type] and enriches them with [context we add] so that
upstream retry logic / alerting / dead-letter queues receive the
information they need to respond correctly.

We deliberately do not swallow exceptions here — the caller depends on
the raise to trigger [retry / alert / compensation].
"""
```

---

## Phase 3: Inline Comment Standards

### The Inline Comment Voice

Write inline comments as if you are the engineer who wrote the code, speaking directly to the next engineer who reads it. Not a robot. Not a documentation generator. A human who made a decision and wants the next person to understand it.

**Tone calibration:**
- Explain the constraint: *"We cannot use X here because [third-party behavior]"*
- Name the trap: *"Do not reorder these — [Y] must have [Z] in place before it runs"*
- Surface the history: *"This exists because [system/API/requirement] behaves this way"*
- Flag the extension point: *"If [new type] is added in the future, it belongs here"*
- Acknowledge the ugliness: *"This is not elegant, but [constraint] leaves no better option"*

### Inline Comment Formats by Purpose

**Single-line decision comment:**
```python
# We use getattr here instead of direct attribute access because not all
# filing models implement both identifier fields — this gracefully handles
# the absence of either without raising AttributeError.
identifier = getattr(file_returns, "tenant_id", None) or getattr(...)
```

**Multi-line reasoning block (for significant decision points):**
```python
# ------------------------------------------------------------------
# [SECTION NAME IN CAPS]
# ------------------------------------------------------------------
#
# [Explanation of why this section exists and what makes it distinct
# from other sections in this function.]
#
# [Specific reason this approach was chosen over alternatives. Name
# the alternative that was rejected and why.]
#
# [What would break if someone changed this, and what the next developer
# must preserve.]
#
```

**Future guidance comment:**
```python
# Future note:
# If [condition] changes, this logic will need to be revisited.
# Specifically, [what to look for and what to change].
# Do not assume [current assumption] without checking [how to verify].
```

**Ordering constraint comment:**
```python
# Order is load-bearing here.
# [Step A] must run before [Step B] because [Step B] depends on [state
# that Step A establishes]. Reordering these will not raise an exception
# but will produce incorrect [output/state/behavior].
```

**External constraint comment:**
```python
# [Third-party system / API / regulation] requires this.
# [Specific behavior of the external system that forces this implementation.]
# This is not how we would design this internally — it reflects their contract.
```

**Guard clause comment:**
```python
# We exit early here rather than processing because [reason].
# The caller is expected to have validated [condition] before reaching this
# point. If [condition] is false here, it represents a caller error, not a
# recoverable state.
```

---

## Phase 4: Language-Specific Patterns

### Python

```python
# Docstrings: triple-quoted, immediately after def/class
# Inline: single # with space, full sentences
# Section dividers: use ── or -- style for visual grouping of major branches

def method(self, param: Type) -> ReturnType:
    """
    [Summary]

    Why this exists:
    ----------------
    [Reasoning]
    """
    # [Inline comment for non-obvious logic]
    result = operation()

    # ── SECTION NAME ──────────────────────────────────────────────────────
    # [Section-level explanation for significant branches]
    if condition:
        pass
```

### TypeScript / JavaScript

```typescript
// JSDoc for public APIs, class methods, and exported functions
// Inline: // with full sentences
// Section dividers: // ── or // --- for major branches

/**
 * [Summary]
 *
 * Why this exists:
 * ----------------
 * [Reasoning]
 *
 * @param paramName - [What it represents, not just its type]
 * @returns [What the value means, not just its type]
 * @throws {ErrorType} [When and why]
 */
function method(param: Type): ReturnType {
  // [Inline comment for non-obvious logic]
  const result = operation();

  // ── SECTION NAME ────────────────────────────────────────────────────
  // [Section-level explanation]
  if (condition) {
    // [Branch-specific reasoning]
  }
}
```

### Go

```go
// Package comments: full sentences, explain the package's purpose and scope
// Function comments: start with function name, explain why it exists
// Inline: // with full sentences

// MethodName [summary — starts with the function name by Go convention].
//
// Why this exists:
// [Reasoning in plain paragraphs]
//
// [Caller must know / implementer must not violate]
func (s *Service) MethodName(param Type) (ReturnType, error) {
    // [Inline comment for non-obvious logic]
    result, err := operation()

    // We handle this error explicitly rather than wrapping generically
    // because [upstream caller depends on specific error type for retry logic].
    if err != nil {
        return nil, fmt.Errorf("methodName: %w", err)
    }
}
```

### Rust

```rust
/// [Summary]
///
/// # Why this exists
///
/// [Reasoning — what problem this solves, what it would mean to remove it]
///
/// # Arguments
///
/// * `param` - [What it represents, constraints on its value]
///
/// # Errors
///
/// Returns [`ErrorType`] when [condition]. The caller should [what to do].
///
/// # Panics
///
/// Panics if [condition] — this represents a programming error, not a
/// recoverable state.
fn method(param: Type) -> Result<ReturnType, ErrorType> {
    // [Inline comment for non-obvious logic]
    
    // SAFETY: [If using unsafe, always explain the invariant that makes this safe]
    let result = unsafe { operation() };
}
```

### C++ (Systems / AI/ML)

```cpp
/**
 * @brief [Summary]
 *
 * Why this exists:
 * ----------------
 * [Reasoning]
 *
 * @param param [What it represents, ownership semantics, nullability]
 * @return [What the value means, ownership transfer if heap-allocated]
 * @throws [ExceptionType] [When and why]
 *
 * @note [Threading safety, performance characteristics, side effects]
 */
ReturnType methodName(Type param) {
    // [Inline comment for non-obvious logic]
    
    // Order is load-bearing: [explanation]
    step_a();
    step_b();  // Depends on state established by step_a
}
```

---

## Phase 5: Comment Quality Gates

Before finalizing any comment, pass it through these filters:

### The Five Filters

**Filter 1 — Does it say something the code cannot?**
If the comment says "increment counter by 1" next to `counter++`, delete it.
If the comment says "we increment here and not at insertion because the reconciliation job reads this value before inserts are flushed", keep it.

**Filter 2 — Is it honest?**
Does the comment reflect what the code actually does? Outdated comments are worse than no comments — they actively mislead. If commenting reveals that the code does something the developer did not intend, flag it.

> "Note: Based on this code, [what it actually does]. If the intent was [what the name/docstring suggests], there may be a logic error at [line]. Please verify before merging this comment."

**Filter 3 — Does it sound like a person?**
Read it aloud. Would a senior engineer say this to a colleague? If it sounds like a documentation generator, rewrite it. Real engineers say "we can't use X here because Y breaks" — not "X is not utilized due to Y incompatibility."

**Filter 4 — Is it at the right level?**
- Docstrings explain the function's existence and contract.
- Section comments explain a block's role in the function.
- Inline comments explain a specific line's non-obvious choice.
Don't put line-level explanations in docstrings. Don't put function-level reasoning in inline comments.

**Filter 5 — Does it help a developer who will change this in 6 months?**
The reader is not reading this to understand what the code does today. They are reading it because they need to change something. Does the comment help them understand what they can safely change, what they must not touch, and what they must test if they do change it?

---

## Phase 6: Output Format

Present the commented code in this format:

````markdown
# Code Commentary

**Language**: [Language]
**Unit**: [Function / Class / Module]
**Comment Strategy**: [Brief description of what was commented and why]

---

## Commented Code

```[language]
[Full commented code — the complete unit, not excerpts]
```

---

## Commentary Decisions

[For each significant comment added, a brief rationale:]

**[Function/line reference]** — [Why this comment was written, what decision it surfaces]

**[Function/line reference]** — [Why this comment was written]

---

## Flags for Developer Review

[Any issues discovered during commenting that the developer should address:]

⚠️  **[Issue]**: [Location] — [Description of the concern]
    [What to verify or fix]

[If no issues found, omit this section]

---

## What Was Not Commented

[Brief explanation of lines/blocks deliberately left without comments and why —
confirms that the omissions were intentional, not missed]
````

---

## Phase 7: Special Scenarios

### Commenting Legacy Code

When code has unclear intent:

```python
# NOTE: The original intent of this block is unclear from context alone.
#
# Based on the behavior observed:
# [What the code appears to do]
#
# If this is intentional, the comment above captures the behavior.
# If this is a bug or incomplete implementation, it should be reviewed
# before this code is modified further.
```

### Commenting Workarounds

When code works around a bug, limitation, or external quirk:

```python
# WORKAROUND: [Third-party library / external system] does not [expected behavior].
#
# Specifically: [exact behavior that forced this workaround]
#
# This can be removed when [condition — library version / API change / etc.].
# Tracked in: [issue/ticket reference if available]
```

### Commenting Security-Sensitive Code

```python
# SECURITY: [What makes this code security-sensitive]
#
# [What property this code maintains and why it matters]
#
# Do not modify this without understanding:
#   - [Invariant 1 that must be preserved]
#   - [Invariant 2 that must be preserved]
#
# If [invariant] is broken, the consequence is [specific vulnerability/risk].
```

### Commenting Financial / Compliance Logic

```python
# COMPLIANCE: [Regulatory requirement or business rule this implements]
#
# [The specific rule, limit, or calculation required]
#
# This is not arbitrary — it reflects [CBN regulation / PCI requirement /
# internal policy / contractual obligation].
#
# Changes to this logic require [sign-off / compliance review / legal review].
```

### Commenting AI/ML Code

```python
# MODEL BEHAVIOR NOTE:
# [What the model does here and why this specific approach was chosen]
#
# [Known limitations of this approach and the conditions under which
# it may produce unexpected outputs]
#
# [What to monitor in production to detect if this is degrading]
```

---

## Notes
- **NON-NEGOTIABLE — THIS SKILL DOES NOT CHANGE CODE.** Not a single variable name. Not a single function signature. Not a single line of logic. Not a class, method, import, or expression. The only additions permitted are comments (`#`, `//`, `/* */`) and docstrings. If the temptation arises to "clean up" something while commenting — resist it, flag it, and leave the code exactly as found. Any output where the code itself differs from the input is a failure of this skill, regardless of whether the change was an improvement.
- Never rewrite or refactor the code while commenting it. Comment what exists. If commenting reveals a bug or design concern, flag it in the developer review section — do not silently fix it.
- Never add a comment to every line. Commented code has more comments on the hard lines, none on the obvious lines. Uniform commenting is a sign that no judgment was applied.
- When the code does something wrong or unclear, say so in the comment. Honest comments are more valuable than polished ones.
- For large files or modules, ask the user which sections to prioritize rather than producing shallow comments on everything.
- Docstrings are written for the function's public interface — what callers need to know. Inline comments are written for the function's body — what implementers need to know. These are different audiences. Write accordingly.
- If the language has a documentation standard (PEP 257, JSDoc, godoc, rustdoc), follow it for structure. But do not let the format constrain the voice — the standard tells you where to put things, not what to say.
```

---