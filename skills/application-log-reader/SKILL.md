---
name: application-log-reader
description: Use this skill whenever a user needs to analyze, parse, trace, or debug application logs from any environment. Trigger when users say things like “read my logs,” “analyze these logs,” “find the error,” “debug this output,” “my app is crashing,” “something failed in production,” “trace this stack trace,” or “help me find the root cause.” Also activate when users paste raw logs, stack traces, crash reports, runtime output, or upload log files from GCP Cloud Run, Docker, Kubernetes, VMs, local machines, or other runtimes. If logs are shared alongside a codebase, use both together. This skill performs deep forensic debugging — not surface scanning. It correlates logs with source code to pinpoint the exact failure origin down to the file, module, function, and execution path, then prescribes precise fixes, likely causes, and remediation steps.
---

# Application Log Reader

A skill for forensic log analysis across all environments and log formats. This skill reads logs the way a senior SRE and senior software engineer read them together — with the codebase open in the other hand, tracing every error to its exact source and every failure to its root cause.

This is not a log summarizer. This is a log hunter.

---

## Core Operating Principles

**Logs are evidence. Treat them like a crime scene.**
Every line has information. Timestamps reveal sequences. Gaps reveal silences. Repeated patterns reveal systemic issues. A single stack trace reveals a call chain. Read everything before concluding anything.

**The error message is rarely the root cause.**
`NullPointerException`, `Connection refused`, `500 Internal Server Error` — these are symptoms. The root cause is three lines earlier, or in a different service, or in a configuration loaded at startup. Find the origin, not the echo.

**Logs without the codebase are half the picture.**
A stack trace with line numbers is a map. The codebase is the territory. When both are available, every error is traceable to exact source code. Use both.

**Correlation over isolation.**
Single log lines are rarely the full story. Errors correlate across time, across services, across threads. Find the sequence that produced the failure, not just the failure itself.

---

## Phase 0: Intake Protocol

### Step 0.1 — Identify what has been provided

```
Intake Assessment
=================
Logs Provided:       [Yes / No / Partial]
Codebase Provided:   [Yes / No / Partial]
Log Source:          [VM / GCP Cloud Run / Local / Docker / K8s / Unknown]
Log Format:          [JSON structured / Plaintext / Mixed / Unknown]
Time Range:          [If detectable from timestamps]
Services/Apps:       [If multi-service logs, how many and which]
Error Type Stated:   [What the user says is wrong, if anything]
```

### Step 0.2 — Ask for missing context (single message, only what is needed)

If critical context is missing:

```
To analyze these logs effectively:

1. **Log Source** (if not clear): Where are these logs from?
   (GCP Cloud Run / Compute Engine VM / Local machine / Docker / Kubernetes / Other)

2. **Codebase** (if not provided): Can you share the codebase or the 
   relevant modules? With source code I can trace errors to exact lines.

3. **What you observed**: What symptom triggered this investigation?
   (Crash / Slow response / Wrong output / User report / Alert fired)

4. **Time of incident** (if relevant): When did this start?
   (Helps filter log volume to the relevant window)
```

Do not ask for anything already provided in context.

---

## Phase 1: Log Ingestion & Format Detection

### Supported Log Sources & Their Characteristics

**GCP Cloud Run**
```
Format indicators:
- JSON structured: {"severity":"ERROR","message":"...","timestamp":"...","trace":"..."}
- Fields: severity, message, timestamp, labels, httpRequest, trace, spanId
- Severity levels: DEFAULT, DEBUG, INFO, NOTICE, WARNING, ERROR, CRITICAL, ALERT, EMERGENCY
- Container stdout/stderr captured automatically
- Request logs separate from application logs
- Cold start events: "Starting HTTP server on port..."

Key fields to extract:
- trace: links logs across a single request
- labels.instanceId: identifies which container instance
- httpRequest.status: HTTP response code
- httpRequest.latency: response time
```

**GCP Compute Engine VM / Linux VM**
```
Format indicators:
- Syslog: "Jan 15 10:23:45 hostname appname[pid]: message"
- Journald: structured via journalctl output
- Application logs: depends on app logging framework
- System logs: /var/log/syslog, /var/log/messages
- App-specific: /var/log/[appname]/*.log

Key patterns:
- OOM kills: "Out of memory: Kill process"
- Disk full: "No space left on device"
- Permission errors: "Permission denied"
- Segfaults: "segfault at..."
- Systemd failures: "Failed to start...", "Unit ... entered failed state"
```

**Local Machine / Development**
```
Format indicators:
- Mixed: depends entirely on application and framework
- Often less structured than production
- May include debug output, print statements
- Stack traces in development are often more verbose
- Hot reload / watch mode output mixed with app output
```

**Docker / Container**
```
Format indicators:
- docker logs output: includes container timestamps
- Format: "2024-01-15T10:23:45.123456789Z stdout F [message]"
- Multi-container: docker-compose prefixes service name
- Health check output distinct from application output
```

**Kubernetes**
```
Format indicators:
- kubectl logs output
- Pod name and namespace in context
- Multiple containers per pod possible
- Previous container logs: "--previous" flag context
- Events separate from container logs
```

### Log Format Parser

Detect and normalize the log format before analysis:

```
Log Format Detection
====================
Detected Format:     [JSON structured / Plaintext / Syslog / Mixed]
Timestamp Format:    [ISO8601 / Unix epoch / Syslog / None detected]
Log Level Field:     [severity / level / lvl / LEVEL / none]
Message Field:       [message / msg / log / body]
Service Field:       [service / app / name / labels.app]
Trace/Request ID:    [trace / request_id / correlation_id / X-Request-ID]
Line Count:          [N lines total]
Time Span:           [First timestamp → Last timestamp]
Services Detected:   [List of distinct services/apps in logs]
```

---

## Phase 2: Error Isolation & Classification

### The Five-Pass Read

Execute all five passes before producing output.

---

**Pass 1 — Severity Extraction**

Extract every log line at ERROR level or above. In order of criticality:

```
CRITICAL / FATAL / EMERGENCY / ALERT
  → System is down or data is at risk. Immediate action required.

ERROR
  → A request or operation failed. User impact likely.

WARNING
  → Something unexpected happened. Did not fail yet. Will fail soon.

INFO (anomalous)
  → Expected info messages that appear in unexpected context or frequency.
  → Example: "Reconnecting to database..." appearing 400 times in 10 minutes is not INFO — it is an ERROR.
```

---

**Pass 2 — Stack Trace Extraction**

Every stack trace in the logs is extracted in full — never truncated. Stack traces are the most precise evidence available.

```
Stack Trace Registry
====================

[TRACE-001]
Timestamp:  [exact timestamp]
Thread/Goroutine/Process: [if available]
Exception/Error Type: [ExceptionClass / error string]
Message: [error message]
Full Trace:
  [complete stack trace, every frame]
First Occurrence: [timestamp]
Last Occurrence: [timestamp]
Occurrence Count: [N times in log window]
```

---

**Pass 3 — Sequence Reconstruction**

For each identified error, reconstruct the event sequence that led to it:

```
Incident Sequence: [Error name / description]
==============================================
T+0:00  [First relevant log line — what started this]
T+0:01  [Next relevant event]
T+0:03  [...]
T+0:05  [The failure itself]
T+0:06  [Downstream effects / cascading failures]
T+0:12  [Recovery attempt if any / further failure]

Root Event: [The first thing that happened that led to this chain]
Trigger:    [What caused the root event]
```

---

**Pass 4 — Pattern Detection**

Look for patterns across the full log window:

```
Pattern Analysis
================

Frequency Anomalies:
- [Event]: occurring [N] times/minute (baseline: [M] times/minute)

Error Clustering:
- [Error type] clusters at [time ranges] — suggests [scheduled job / traffic spike / deployment]

Correlated Failures:
- [Error A] consistently followed by [Error B] within [N]ms — suggests causation

Resource Signals:
- Memory: [OOM signals / GC pressure indicators]
- CPU: [Timeout patterns suggesting CPU saturation]
- Disk: [No space left / slow I/O indicators]
- Network: [Connection refused / timeout / DNS failure patterns]

Cold Start / Restart Signals:
- [Service] restarted at [timestamps] — [N] restarts in window
```

---

**Pass 5 — Silence & Gap Analysis**

What is missing from the logs is often as important as what is present:

```
Gap Analysis
============
Expected log lines not found:
- [e.g., "Health check response" expected every 30s — absent from T+10:00 to T+10:45]
- [e.g., "Job completed" never appears after "Job started" at T+09:15]

Service silence periods:
- [Service name]: no logs from [T+X] to [T+Y] — [N] second gap
  Possible causes: [crash / network partition / thread deadlock / CPU saturation]

Request traces opened but never closed:
- [request_id / trace_id]: started at [T] — no completion log found
```

---

## Phase 3: Codebase Cross-Reference (The Hunt)

When the codebase is provided alongside logs, this phase activates. This is where errors become locatable to exact source lines.

### Step 3.1 — Codebase Orientation

```bash
# Map the full codebase structure
find . -type f | grep -v node_modules | grep -v .git | grep -v dist | 
  grep -v __pycache__ | grep -v .pyc | sort

# Identify the language and framework
find . -name "*.py" -o -name "*.ts" -o -name "*.go" -o -name "*.java" | 
  sed 's/.*\.//' | sort | uniq -c | sort -rn

# Find logging configuration — how logs are emitted
find . -name "*.py" -o -name "*.ts" -o -name "*.go" | 
  xargs grep -l "logger\|logging\|log\." 2>/dev/null

# Find error handling patterns
grep -rn "try\|catch\|except\|panic\|recover\|throw" --include="*.py" \
  --include="*.ts" --include="*.go" . | head -50
```

### Step 3.2 — Error String Hunting

For every error message extracted from logs, hunt its origin in the codebase:

```bash
# Search for exact error strings
grep -rn "[exact error message from log]" .

# Search for partial matches (error messages often formatted)
grep -rn "could not connect" --include="*.py" --include="*.ts" --include="*.go" .

# Search for the exception class / error type
grep -rn "class DatabaseConnectionError\|DatabaseConnectionError(" .

# Find where this error is raised / thrown / returned
grep -rn "raise DatabaseConnectionError\|throw new DatabaseConnectionError\|
  errors.New(\"could not connect\")" .
```

### Step 3.3 — Stack Trace Resolution

For every stack frame in extracted stack traces:

```
Frame Resolution: [stack frame text]
=====================================
Raw Frame:    "at processRequest (src/api/handlers/user.ts:142:18)"
File:         src/api/handlers/user.ts
Line:         142
Column:       18
Function:     processRequest

→ Reading src/api/handlers/user.ts lines 135-155...
→ Line 142: [exact code at that line]
→ Context: [what this function does, what it was doing at line 142]
→ The failure: [what went wrong at this exact point]
```

### Step 3.4 — Call Chain Reconstruction

Trace backward from the failure through the call chain:

```
Call Chain Analysis
===================
Entry Point:     [HTTP handler / cron trigger / queue consumer / etc.]
  ↓ called
[Function A]     [file:line]
  ↓ called
[Function B]     [file:line]
  ↓ called
[Function C]     [file:line]  ← FAILURE POINT
  ↓ raised
[Error type]     [propagated to...]
  ↓ caught at
[Handler]        [file:line — where it was caught or where it bubbled to surface]
```

### Step 3.5 — Precision Location Report

For each identified issue, produce:

```
ISSUE LOCATED
=============
Log Evidence:
  "[exact log line(s) that evidence this issue]"
  Timestamp: [T]
  Occurrence: [N times]

Source Location:
  File:     [exact relative file path]
  Folder:   [folder name and its role in the architecture]
  Module:   [module / package / service name]
  Function: [exact function/method name]
  Line:     [exact line number]
  Code:     
    [line N-3]: [code for context]
    [line N-2]: [code for context]
    [line N-1]: [code for context]
  → [line N  ]: [THE FAILING LINE — highlighted]
    [line N+1]: [code for context]
    [line N+2]: [code for context]

Why It Failed:
  [Precise technical explanation of why this line/logic failed]

Triggered By:
  [What upstream condition caused execution to reach this point in a failing state]
```

---

## Phase 4: Root Cause Analysis

After all passes and codebase cross-referencing, produce the root cause determination.

### Root Cause Report Format

```
Root Cause Analysis
===================

INCIDENT SUMMARY
----------------
Symptom:        [What the user saw / what alerted]
First Failure:  [Timestamp of first evidence]
Impact Duration:[How long before recovery / still ongoing]
Blast Radius:   [What failed as a result — users affected, operations failed]

ROOT CAUSE
----------
Classification: [Code Bug / Configuration Error / Infrastructure Failure / 
                 Dependency Failure / Resource Exhaustion / Data Issue /
                 Race Condition / Deployment Error / Security Event]

The Root Cause:
  [Precise, specific statement of what went wrong]
  
  NOT: "The database connection failed"
  YES: "The connection pool max_connections was set to 5 in config/database.ts:23,
        but the application spawns 8 worker threads at startup (src/server.ts:67),
        each attempting to acquire a connection. Under any load, 3 workers
        permanently block waiting for a connection that never becomes available,
        causing request timeouts after the 30s timeout configured in 
        src/middleware/timeout.ts:12."

Evidence Chain:
  1. [Log line / code location] — [What it proves]
  2. [Log line / code location] — [What it proves]
  3. [Log line / code location] — [What it proves]

CONTRIBUTING FACTORS
--------------------
1. [Secondary issue that made the root cause worse or harder to detect]
2. [Missing safeguard that would have caught this earlier]
3. [Configuration or code that amplified the impact]

WHAT DID NOT CAUSE IT
---------------------
[Red herrings — errors in the logs that look related but are not the cause]
[This prevents wasted engineering effort chasing the wrong thing]
```

---

## Phase 5: Fix Prescriptions

For every identified issue, produce an exact, implementable fix.

### Fix Format

````markdown
## Fix: [Issue Title]

**Severity**: [Critical / High / Medium / Low]
**Effort**: [Minutes / Hours / Days]
**Risk**: [Safe / Low / Medium / High — risk of fix introducing new issues]

### Root Cause (one line)
[The single sentence that describes what is wrong]

### Exact Location
- File: `[path/to/file.ext]`
- Line: [N]
- Function: `[functionName]`

### Current Code (the problem)
```[language]
// Line N-2
[context line]
// Line N-1  
[context line]
// Line N — THE PROBLEM
[exact failing code]
// Line N+1
[context line]
```

### Fixed Code
```[language]
// Line N-2
[context line unchanged]
// Line N-1
[context line unchanged]  
// Line N — FIXED
[corrected code]
// Additional lines if fix requires them
[new code]
// Line N+1
[context line unchanged]
```

### Why This Fix Works
[Precise technical explanation of why the fix resolves the root cause]

### Verification
After deploying this fix, verify with:
- [ ] [Specific log line or metric that should appear if fix is working]
- [ ] [Specific error that should no longer appear]
- [ ] [Test case that exercises the fixed path]

### Potential Side Effects
[Anything this fix might affect that needs to be checked]

### If This Fix Is Not Immediately Possible
[Mitigation or workaround that reduces impact while the fix is prepared]
````

---

## Phase 6: Log Analysis Report

The full output of every analysis session.

````markdown
# Log Analysis Report

**Source**: [VM / GCP Cloud Run / Local / Docker / K8s]
**Analysis Date**: [Date]
**Log Window**: [Start timestamp → End timestamp]
**Log Volume**: [N lines / N MB]
**Codebase Provided**: [Yes / No]

---

## Executive Summary

**Status**: [🔴 Active Incident / 🟠 Resolved but Root Cause Unaddressed / 
             🟡 Degraded / 🟢 Healthy with Warnings]

[2–4 sentences: what happened, what broke, what the root cause is, what to do first]

---

## Error Inventory

| # | Error | Severity | First Seen | Count | Root Cause Found |
|---|-------|----------|------------|-------|-----------------|
| 1 | [Error name] | Critical | [T] | [N] | Yes / No |
| 2 | | | | | |

---

## 🔴 Critical Errors

### Error 1: [Error Title]

**Log Evidence**:
```
[exact log lines]
```

**Source Location** (if codebase available):
- File: `[path]`
- Line: [N]
- Function: `[name]`
- Code: `[exact line]`

**Root Cause**: [Precise explanation]

**Fix**: [See Fix section below / inline fix]

---

## 🟠 High Priority Errors

[Same format]

---

## 🟡 Warnings & Anomalies

[Same format, abbreviated]

---

## Pattern Analysis

[Output of Pass 4 — Pattern Detection]

---

## Sequence of Events

[Timeline reconstruction of the incident]

---

## Root Cause Analysis

[Full RCA from Phase 4]

---

## Fixes (Priority Order)

[All fixes from Phase 5, ordered by severity then effort]

---

## Infrastructure Signals

[Any signals about the environment itself — not the application code]

Memory:   [OOM / pressure / healthy]
CPU:      [Saturation signals / healthy]  
Disk:     [Space / I/O signals / healthy]
Network:  [Timeout / DNS / connectivity signals / healthy]
Restarts: [Container/process restart count and triggers]

---

## What To Monitor After Fixes

| Signal | Tool | Threshold | Why |
|--------|------|-----------|-----|
| [metric] | [logging query / metric] | [value] | [what it confirms] |

---

## Red Herrings (Do Not Chase)

[Errors in the logs that look suspicious but are not related to the incident]
[Saves engineering time from going down wrong paths]
````

---

## Phase 7: Environment-Specific Analysis Modules

### GCP Cloud Run — Specific Analysis

```
GCP Cloud Run Checks
====================

Cold Start Analysis:
- Cold starts detected: [N in window]
- Cold start latency: [min / avg / max]
- Trigger: [traffic pattern / scaling event]
- Impact: [requests that experienced cold start latency]

Instance Scaling:
- Min instances: [if detectable from logs]
- Scale-up events: [timestamps]
- Scale-down events: [timestamps]
- Request queuing signals: [if detected]

Memory Limit Signals:
- Container OOM: [detected / not detected]
- Memory pressure: [detected / not detected]
- Signal: "Container killed" or exit code 137

Request Log Correlation:
- Linking httpRequest logs to application logs via trace field
- Failed requests (5xx): [N out of M total in window]
- Slow requests (>threshold): [N — latency distribution]

Cloud Run Specific Errors to flag:
- "container failed to start" — startup probe failure
- "revision failed to become ready" — deployment failure  
- "request timeout" — 60min Cloud Run max exceeded
- exit code 137 — OOM killed
- exit code 1 — application error on startup
```

### VM / Compute Engine — Specific Analysis

```
VM Environment Checks
=====================

System-Level Events:
- OOM killer invocations: grep "Out of memory" /var/log/syslog
- Disk full: grep "No space left" /var/log/syslog
- Network interface issues: grep "eth0\|ens4" /var/log/syslog
- Segfaults: grep "segfault" /var/log/syslog

Process Management:
- Systemd unit failures: grep "Failed\|failed" /var/log/syslog
- Process restarts: grep "Started\|Stopped" /var/log/syslog | [service name]
- Core dumps: find /var/crash -name "core.*" or coredumpctl list

Resource Exhaustion Signals:
- File descriptor limits: "Too many open files"
- Thread limits: "pthread_create failed"
- Port exhaustion: "bind: address already in use" repeated
- Inode exhaustion: "No space left on device" with disk space available

SSH/Auth Events (if security investigation):
- Failed logins: grep "Failed password\|Invalid user" /var/log/auth.log
- Sudo usage: grep "sudo" /var/log/auth.log
```

### Local Machine — Specific Analysis

```
Local Environment Checks
========================

Development-Specific Patterns:
- Hot reload errors: framework-specific restart messages
- Port conflicts: "EADDRINUSE\|address already in use"
- Missing environment variables: "undefined\|not set\|required"
- Missing dependencies: "Cannot find module\|No module named\|package not found"
- Permission errors on local files: "EACCES\|Permission denied"

Framework-Specific Startup Errors:
- Node.js: uncaughtException at startup
- Python: ImportError / ModuleNotFoundError at startup
- Go: panic on init()
- Database connectivity: connection refused to localhost
```

---

## Phase 8: Log Query Generation

After analysis, generate queries the user can run to monitor for recurrence.

### GCP Cloud Logging Queries

```
# All errors in Cloud Run service
resource.type="cloud_run_revision"
resource.labels.service_name="[service-name]"
severity>=ERROR

# Specific error pattern
resource.type="cloud_run_revision"
textPayload=~"[error pattern from analysis]"

# Request failures
resource.type="cloud_run_revision"
httpRequest.status>=500

# Memory/OOM
resource.type="cloud_run_revision"
(textPayload=~"out of memory" OR textPayload=~"OOM" OR jsonPayload.message=~"killed")

# Cold starts
resource.type="cloud_run_revision"  
textPayload=~"Starting"
```

### Linux / VM Log Commands

```bash
# Real-time error following
sudo journalctl -u [service-name] -f --no-pager | grep -i "error\|fatal\|critical"

# Last N lines with errors only
sudo journalctl -u [service-name] --since "1 hour ago" | grep -E "ERROR|FATAL|CRITICAL"

# Syslog errors
sudo grep -i "error\|fatal\|oom\|killed" /var/log/syslog | tail -100

# Application-specific log file
tail -f /var/log/[appname]/app.log | grep --line-buffered -E "ERROR|FATAL"
```

### Application Log Grep Patterns

```bash
# Extract all errors with context (3 lines before and after)
grep -n -B3 -A3 "ERROR\|FATAL\|CRITICAL\|panic\|exception" app.log

# Count error types
grep -oP "(ERROR|FATAL|CRITICAL)[^\n]*" app.log | sort | uniq -c | sort -rn

# Find errors in time window
awk '/2024-01-15 10:00/,/2024-01-15 11:00/' app.log | grep "ERROR"

# Extract stack traces (Python)
grep -A 20 "Traceback (most recent call last)" app.log

# Extract stack traces (Node.js)
grep -A 15 "Error:" app.log | grep -v "^--$"
```

---

## Tooling Reference

```bash
# Parse JSON logs (when logs are structured JSON, one object per line)
cat app.log | python3 -m json.tool | grep -A2 '"severity": "ERROR"'

# jq for structured log analysis
cat app.log | jq 'select(.severity == "ERROR") | {time: .timestamp, msg: .message}'
cat app.log | jq -r 'select(.severity >= "ERROR") | [.timestamp, .severity, .message] | @tsv'

# Count by severity
cat app.log | jq -r '.severity' | sort | uniq -c | sort -rn

# Extract unique error messages
cat app.log | jq -r 'select(.severity == "ERROR") | .message' | sort -u

# Find slowest requests (Cloud Run JSON logs)
cat app.log | jq 'select(.httpRequest.latency != null) | 
  {latency: .httpRequest.latency, url: .httpRequest.requestUrl}' | 
  sort -t'"' -k4 -rn | head -20

# Log volume by minute (detect spikes)
cat app.log | grep -oP '"\d{4}-\d{2}-\d{2}T\d{2}:\d{2}' | sort | uniq -c
```

---

## Notes

- Never skim. Read every ERROR and FATAL line in full before concluding anything.
- When stack traces are present and the codebase is available, always resolve every frame to exact source code. The line number in a stack trace is a direct pointer — use it.
- When the codebase is not available, say so clearly and flag which conclusions are definitive versus which require source code to confirm.
- Red herrings are as important as root causes. Explicitly calling out what did NOT cause the incident saves hours of engineering time.
- If log volume is very high (millions of lines), ask the user to filter to the incident time window first, or to provide the specific error-containing sections.
- When the same error appears thousands of times, sample the first, middle, and last occurrences — the message often evolves in ways that reveal progression of the failure.
- Always generate monitoring queries at the end — the goal is not just to fix the current incident but to detect the next one before it becomes an incident.
- For security-related log events (auth failures, unusual access patterns, data exfiltration signals), flag them separately and escalate their priority regardless of application impact.
```

---