# Claude Skills for Engineering & Product Teams

A curated library of **12 production-grade Claude Skills** built at TaxStreem for engineering, QA, product, and leadership work — code review, debugging, QA gating, audits, architecture thinking, Linear ticket writing, design systems, and more.

Drop them into Claude Code (CLI or Desktop) or the Claude app, and Claude automatically picks the right skill when your request matches it. No prompt engineering required — just describe what you need.

---

## Table of Contents

- [What is a Claude Skill?](#what-is-a-claude-skill)
- [Skills at a Glance](#skills-at-a-glance)
- [Which Skill Should I Use?](#which-skill-should-i-use)
- [Repository Structure](#repository-structure)
- [Installation](#installation)
  - [Option A — Claude Code CLI (plugin marketplace)](#option-a--claude-code-cli-plugin-marketplace-recommended)
  - [Option B — Claude Code CLI (manual copy)](#option-b--claude-code-cli-manual-copy)
  - [Option C — Claude Code Desktop](#option-c--claude-code-desktop)
  - [Option D — Claude app (claude.ai / Desktop chat) for product teams](#option-d--claude-app-claudeai--desktop-chat-for-product-teams)
- [Using the Skills](#using-the-skills)
- [Skill Details & Example Prompts](#skill-details--example-prompts)
- [Tips for Getting the Best Results](#tips-for-getting-the-best-results)
- [Troubleshooting](#troubleshooting)
- [Contributing a New Skill](#contributing-a-new-skill)

---

## What is a Claude Skill?

A **Skill** is a folder containing a `SKILL.md` file: YAML front-matter (`name`, `description`) followed by detailed instructions. Claude reads the short `description` of every installed skill, and when your request matches one, it loads the full instructions and follows them.

```text
skills/senior-qa-engineer/
├── SKILL.md      ← what Claude reads (name + description + playbook)
└── README.md     ← what humans read (overview + example prompts)
```

Think of each skill as a **specialist playbook** — a senior QA engineer, a staff-level reviewer, a CTO's chief of staff — that Claude adopts on demand, producing consistent, structured output every time.

---

## Skills at a Glance

### 🔍 Code Review & Quality

| Skill | What it does | Primary audience |
|---|---|---|
| [`ai-code-reviewer`](skills/ai-code-reviewer) | Forensic audit of **AI-generated** code — hallucinated APIs, confident incorrectness, subtle logic bugs, production-unsafe patterns. Returns corrected code + tests. | Engineers |
| [`staff-engineer-production-review`](skills/staff-engineer-production-review) | Staff-level PR / code review with a **SHIP / SHIP WITH FIXES / BLOCK** verdict, severity-ranked findings and a scored rubric. | Engineers, Tech Leads |
| [`senior-qa-engineer`](skills/senior-qa-engineer) | Verifies a codebase against a **PRD/spec**, walks user journeys end-to-end, hunts breaking changes, and delivers a one-screen release verdict. | QA, Engineers, PMs |
| [`audit-and-remediate`](skills/audit-and-remediate) | Structured **fintech codebase audit** (Go, TS/Node, Python, Rust, C++) scored across six dimensions, with a `file:line` remediation roadmap. Read-only — never edits code. | Tech Leads, Security |

### 🛠️ Build, Debug & Document

| Skill | What it does | Primary audience |
|---|---|---|
| [`senior-aiml-engineering-assistant`](skills/senior-aiml-engineering-assistant) | Senior SWE + AI/ML engineer that **executes** in your repo: brief → approval → tests first → code → `codebaseMemory.md` + `humanReview.md`. | Engineers, ML Engineers |
| [`application-log-reader`](skills/application-log-reader) | Forensic log & stack-trace debugging (Cloud Run, Docker, K8s, VMs, local), correlated to the exact file/function, with precise fix prescriptions. | Engineers, SRE / On-call |
| [`code-commenter`](skills/code-commenter) | Adds docstrings and inline comments in a senior-engineer voice — the **why**, rejected alternatives, and what must not break. Never rewrites logic. | Engineers |

### 🧭 Product, Design & Leadership

| Skill | What it does | Primary audience |
|---|---|---|
| [`cto-digital-assistant`](skills/cto-digital-assistant) | CTO-level intelligence: **implementation fidelity reports** (code vs PRD/SRS/RFC), milestones, sprint planning, tech-debt and board updates. | CTO, Eng Managers, PMs |
| [`fintech-product-manager-developer-ticket-creator`](skills/fintech-product-manager-developer-ticket-creator) | Turns Slack threads, emails, and meeting notes into **compliance-aware Linear tickets** with Nigerian tax context (NTA 2025, FIRS, CIT, PIT, VAT). | PMs, Eng Managers |
| [`design-system-architect`](skills/design-system-architect) | **Creates** a complete design system (tokens, type, colour, components) or **reviews** an existing codebase/URL and prescribes a better one. | Designers, Frontend, PMs |
| [`brainstorming-colleague`](skills/brainstorming-colleague) | A rigorous thinking partner for ambiguous, high-stakes problems — multi-angle reasoning, pushback, trade-off tables, always ending in an **implementation plan**. | Everyone |
| [`content-research-writer`](skills/content-research-writer) | Research-first long-form writing (blogs, LinkedIn, newsletters, whitepapers) using 2024+ sources, in a senior AI/infra engineer's voice. | DevRel, Leadership, PMs |

---

## Which Skill Should I Use?

| I want to… | Use |
|---|---|
| Check code Copilot / Cursor / Claude / ChatGPT wrote | `ai-code-reviewer` |
| Get a go/no-go on a PR before merging | `staff-engineer-production-review` |
| Confirm we built what the PRD says before release | `senior-qa-engineer` |
| Score a whole service and get a hardening roadmap | `audit-and-remediate` |
| Have Claude actually build or fix a feature | `senior-aiml-engineering-assistant` |
| Find the root cause of a crash from logs | `application-log-reader` |
| Make a confusing module understandable | `code-commenter` |
| Report to leadership on what's built vs. planned | `cto-digital-assistant` |
| Turn a messy Slack thread into a Linear ticket | `fintech-product-manager-developer-ticket-creator` |
| Build or fix our UI design tokens & components | `design-system-architect` |
| Think through an architecture or product decision | `brainstorming-colleague` |
| Write a technical blog post or LinkedIn article | `content-research-writer` |

> **Review skills compared:** `ai-code-reviewer` focuses on AI-specific failure modes (hallucinations); `staff-engineer-production-review` is a general PR/deploy gate; `senior-qa-engineer` checks against a PRD; `audit-and-remediate` scores an entire codebase. They complement each other.

---

## Repository Structure

```text
Claude-Skills-for-engineering-teams/
├── README.md                          ← you are here
├── .claude-plugin/
│   └── marketplace.json               ← lets Claude Code install this repo as a plugin marketplace
└── skills/
    ├── ai-code-reviewer/
    │   ├── SKILL.md
    │   └── README.md                  ← overview + example prompts
    ├── application-log-reader/
    ├── audit-and-remediate/
    ├── brainstorming-colleague/
    ├── code-commenter/
    ├── content-research-writer/
    ├── cto-digital-assistant/
    ├── design-system-architect/
    ├── fintech-product-manager-developer-ticket-creator/
    ├── senior-aiml-engineering-assistant/
    ├── senior-qa-engineer/
    └── staff-engineer-production-review/
```

The skills are grouped into two installable plugins:

| Plugin | Skills included |
|---|---|
| `engineering-skills` | ai-code-reviewer, staff-engineer-production-review, senior-qa-engineer, audit-and-remediate, senior-aiml-engineering-assistant, application-log-reader, code-commenter |
| `product-leadership-skills` | cto-digital-assistant, fintech-product-manager-developer-ticket-creator, design-system-architect, brainstorming-colleague, content-research-writer |

---

## Installation

### Prerequisites

- **Claude Code** installed (CLI and/or Desktop) and signed in. See the [Claude Code docs](https://docs.claude.com/en/docs/claude-code/overview).
- **Git**, to clone this repo (manual install only).

Skills can live in two places — pick based on who should get them:

| Scope | Location | Who gets it |
|---|---|---|
| **Personal** | `~/.claude/skills/<skill-name>/` (Windows: `%USERPROFILE%\.claude\skills\<skill-name>\`) | You, in every project |
| **Project** | `<your-repo>/.claude/skills/<skill-name>/` | Everyone who clones that repo — commit it to share with the team |

---

### Option A — Claude Code CLI (plugin marketplace, recommended)

This repo is a Claude Code plugin marketplace, so you can install it without copying files and pick up updates with one command.

```bash
# 1. Start Claude Code
claude

# 2. Inside Claude Code, add this repo as a marketplace
/plugin marketplace add Stosan/Claude-Skills-for-engineering-teams

# 3. Install one or both plugins
/plugin install engineering-skills@taxstreem-claude-skills
/plugin install product-leadership-skills@taxstreem-claude-skills
```

Restart Claude Code if prompted. To pull the latest versions later:

```bash
/plugin marketplace update taxstreem-claude-skills
```

You can also run `/plugin` on its own to browse and manage installed plugins through an interactive menu.

> **Team rollout:** a project can pre-register this marketplace for everyone by adding it to the repo's `.claude/settings.json` (`extraKnownMarketplaces` / `enabledPlugins`). See the Claude Code plugin docs for the exact keys.

---

### Option B — Claude Code CLI (manual copy)

**macOS / Linux**

```bash
git clone https://github.com/Stosan/Claude-Skills-for-engineering-teams.git
cd Claude-Skills-for-engineering-teams

# Personal: all skills, available in every project
mkdir -p ~/.claude/skills
cp -R skills/* ~/.claude/skills/

# …or just one skill
cp -R skills/senior-qa-engineer ~/.claude/skills/

# …or project-level: share with everyone working in a repo
mkdir -p /path/to/your-repo/.claude/skills
cp -R skills/staff-engineer-production-review /path/to/your-repo/.claude/skills/
```

**Windows (PowerShell)**

```powershell
git clone https://github.com/Stosan/Claude-Skills-for-engineering-teams.git
cd Claude-Skills-for-engineering-teams

# Personal: all skills
New-Item -ItemType Directory -Force "$env:USERPROFILE\.claude\skills" | Out-Null
Copy-Item -Recurse -Force .\skills\* "$env:USERPROFILE\.claude\skills\"

# Project-level
New-Item -ItemType Directory -Force "C:\path\to\your-repo\.claude\skills" | Out-Null
Copy-Item -Recurse -Force .\skills\senior-qa-engineer "C:\path\to\your-repo\.claude\skills\"
```

**Verify**: start `claude` and ask:

```text
What skills do you have available?
```

The installed skills should be listed by name.

> **Tip — stay up to date with a symlink** (macOS/Linux): `ln -s "$(pwd)/skills/senior-qa-engineer" ~/.claude/skills/senior-qa-engineer`, then `git pull` updates the skill in place.

---

### Option C — Claude Code Desktop

The Claude Desktop app's **Code** tab runs the same Claude Code engine as the CLI and reads the same configuration, so:

1. **Install once** using Option A (plugin) or Option B (copy into `~/.claude/skills/` or a project's `.claude/skills/`). Skills installed for the CLI are available in Desktop too, and vice versa.
2. Open the **Claude Desktop app** → **Code** tab.
3. Start a new session and **select the local folder** (repository) you want to work in.
4. Type your request in the prompt box. Claude loads the matching skill automatically, or you can name it explicitly (e.g. *"Use the senior-qa-engineer skill to…"*).
5. To confirm what's loaded, ask: *"What skills do you have available?"*

> If a newly copied skill doesn't appear, start a new session (or restart the app) so Claude Code rescans the skills folders.

---

### Option D — Claude app (claude.ai / Desktop chat) for product teams

PMs and non-engineers who don't use Claude Code can upload skills to their Claude account:

1. Zip a single skill folder so `SKILL.md` sits inside a top-level folder:

   ```bash
   cd skills
   zip -r fintech-product-manager-developer-ticket-creator.zip fintech-product-manager-developer-ticket-creator
   ```

   On Windows: right-click the folder → **Send to** → **Compressed (zipped) folder**.
2. In Claude, open **Settings → Capabilities → Skills** (menu names can vary by version) and make sure Skills / code execution is enabled.
3. **Upload** the `.zip`. The skill now triggers in chats when relevant.

> Organisation admins on Team/Enterprise plans can provision skills for the whole workspace instead of each person uploading.

---

## Using the Skills

### 1. Just describe what you want (automatic)

Claude matches your request against each skill's description. These all trigger skills without naming them:

```text
Review this PR before I merge — is it production ready?
Our checkout service crashed on Cloud Run, here are the logs: …
Turn this Slack thread into a Linear ticket: …
```

### 2. Name the skill (explicit)

When two skills could apply, say which one you want:

```text
Use the ai-code-reviewer skill on src/payments/refund.ts — Cursor wrote it.
Use senior-qa-engineer to verify this branch against docs/PRD-v3.md.
```

### 3. Slash command (Claude Code CLI & Desktop)

Installed skills can be invoked directly as slash commands:

```text
/staff-engineer-production-review review the diff on this branch against main
/application-log-reader ./logs/api-2026-11-10.log
```

When installed as a plugin, skills are namespaced by plugin, e.g. `/engineering-skills:senior-qa-engineer`. Type `/` to see what's available.

### 4. Headless / CI (Claude Code CLI)

Run a skill non-interactively — useful in scripts or CI pipelines:

```bash
claude -p "Use the staff-engineer-production-review skill to review the changes in this branch vs main. Output the report only."

git diff main...HEAD | claude -p "Use ai-code-reviewer on this diff. It was generated by Copilot."
```

### Giving Claude context

- **Files & folders:** reference them by path (`src/auth/`, `docs/PRD.md`) or `@`-mention them in Claude Code.
- **Docs:** PRDs, SRS, RFCs, and specs dramatically improve `senior-qa-engineer`, `cto-digital-assistant`, and `audit-and-remediate`.
- **Logs:** paste them, point to a file, or pipe them in (`cat app.log | claude -p "…"`).

---

## Skill Details & Example Prompts

Each skill folder has its own `README.md` with an overview, inputs, outputs, and many more example prompts. A starter prompt for each:

<details>
<summary><b>ai-code-reviewer</b> — forensic review of AI-generated code</summary>

```text
Copilot wrote src/billing/proration.ts. Review it for hallucinated APIs, logic bugs and production safety, then give me a corrected version with tests.
```
→ [Full examples](skills/ai-code-reviewer/README.md)
</details>

<details>
<summary><b>staff-engineer-production-review</b> — SHIP / BLOCK verdict on a change</summary>

```text
Do a staff engineer production review of the diff on this branch vs main. Give me the verdict, scorecard and severity-ranked findings.
```
→ [Full examples](skills/staff-engineer-production-review/README.md)
</details>

<details>
<summary><b>senior-qa-engineer</b> — QA against the PRD</summary>

```text
QA this codebase against docs/PRD-filing-v2.md. Is every requirement implemented end-to-end? Any breaking changes? Give me the release verdict.
```
→ [Full examples](skills/senior-qa-engineer/README.md)
</details>

<details>
<summary><b>audit-and-remediate</b> — scored fintech audit + roadmap</summary>

```text
Audit the services/ledger Go service. Score it across all six dimensions and give me a sequenced remediation roadmap with file:line citations. Don't change any code.
```
→ [Full examples](skills/audit-and-remediate/README.md)
</details>

<details>
<summary><b>senior-aiml-engineering-assistant</b> — build & fix in your repo</summary>

```text
Add idempotency keys to the POST /payments endpoint. Write the tests first, show me the engineering brief before coding, and flag any breaking changes.
```
→ [Full examples](skills/senior-aiml-engineering-assistant/README.md)
</details>

<details>
<summary><b>application-log-reader</b> — root cause from logs</summary>

```text
Our Cloud Run service started returning 500s at 14:05. Here are the logs. Find the root cause in this repo down to the file and function, and prescribe a fix.
```
→ [Full examples](skills/application-log-reader/README.md)
</details>

<details>
<summary><b>code-commenter</b> — explain the why</summary>

```text
Add docstrings and inline comments to src/tax/vat_calculator.py so a new engineer understands why it works this way. Don't change any logic.
```
→ [Full examples](skills/code-commenter/README.md)
</details>

<details>
<summary><b>cto-digital-assistant</b> — implementation fidelity & milestones</summary>

```text
Audit this codebase against docs/PRD.md and docs/SRS.md. Produce an implementation fidelity report: what's built correctly, what diverged, what's missing, and milestones for the next two sprints.
```
→ [Full examples](skills/cto-digital-assistant/README.md)
</details>

<details>
<summary><b>fintech-product-manager-developer-ticket-creator</b> — Linear tickets with tax context</summary>

```text
Turn this Slack thread into a Linear ticket. Flag any VAT or FIRS compliance implications and write clear acceptance criteria: <paste thread>
```
→ [Full examples](skills/fintech-product-manager-developer-ticket-creator/README.md)
</details>

<details>
<summary><b>design-system-architect</b> — create or review a design system</summary>

```text
Review the design system our web app actually uses (apps/web), score it, and prescribe a cleaner token set and component list.
```
→ [Full examples](skills/design-system-architect/README.md)
</details>

<details>
<summary><b>brainstorming-colleague</b> — thinking partner</summary>

```text
Help me think through whether we should move tax computation from synchronous API calls to an event-driven pipeline. Challenge my assumptions and end with an implementation plan.
```
→ [Full examples](skills/brainstorming-colleague/README.md)
</details>

<details>
<summary><b>content-research-writer</b> — research-first long-form content</summary>

```text
Write a LinkedIn article on how we use LLMs to automate tax compliance checks. Audience: fintech CTOs. Research 2024+ sources first.
```
→ [Full examples](skills/content-research-writer/README.md)
</details>

---

## Tips for Getting the Best Results

1. **Give the source of truth.** For QA, CTO, and audit skills, point to the PRD/spec. Without it, Claude can only infer intent.
2. **Scope it.** "Review `services/payments`" beats "review the repo" on large monorepos.
3. **Say what you'll do with the output.** "This goes to the board" or "this is for a junior engineer" changes tone and depth.
4. **Chain skills.** A strong flow for a feature:
   `brainstorming-colleague` → `senior-aiml-engineering-assistant` → `ai-code-reviewer` → `staff-engineer-production-review` → `senior-qa-engineer`.
5. **Expect questions.** Several skills (content writer, CTO assistant, log reader, AI/ML engineer) ask a short set of intake questions or wait for approval before acting. That's by design.
6. **Know what writes files.** `senior-aiml-engineering-assistant` edits code and creates `AIAgentRule.md`, `codebaseMemory.md`, and `humanReview.md`. `audit-and-remediate` and `code-commenter` do not change logic. Review changes before committing.

---

## Troubleshooting

| Problem | Fix |
|---|---|
| Skill doesn't trigger | Name it explicitly ("Use the X skill…") or use its `/slash-command`. Check it appears when you ask *"What skills do you have?"* |
| Skill not listed | Confirm the path is `~/.claude/skills/<name>/SKILL.md` (not nested one level deeper), then start a new session. |
| Wrong skill triggers | Name the one you want. Overlap between review skills is expected. |
| Plugin install fails | Run `/plugin marketplace list` to confirm the marketplace was added; re-run `/plugin marketplace update taxstreem-claude-skills`. |
| Upload rejected in Claude app | Zip the **folder**, not the bare `SKILL.md`; make sure Skills is enabled in Settings. |
| Output is too generic | Add context: files, specs, logs, audience, and constraints. |

---

## Contributing a New Skill

1. Create `skills/<kebab-case-name>/SKILL.md` with front-matter:

   ```markdown
   ---
   name: my-new-skill
   description: What it does and exactly when Claude should use it, including trigger phrases people actually say.
   ---

   # My New Skill
   Step-by-step instructions, output templates, and examples…
   ```

2. Add `skills/<name>/README.md` with an overview and **at least 5 example prompts** (copy an existing one as a template).
3. Add the skill to the right plugin in `.claude-plugin/marketplace.json`.
4. Add a row to the tables in this README.
5. Test it locally (copy into `~/.claude/skills/`) with several realistic prompts, including ones that should **not** trigger it.
6. Open a PR. Never include secrets, customer data, or internal credentials in a skill.

> Tip: ask Claude to *"use the skill-creator skill to help me build a new skill for …"* — it drafts the `SKILL.md`, writes test prompts, and tunes the description for reliable triggering.

---

**Maintained by the Sam Ayo.** Questions or ideas? Open an issue or reach out in the engineering channel.
