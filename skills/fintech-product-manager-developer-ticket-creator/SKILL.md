---
name: fintech-product-manager-developer-ticket-creator
description: "A skill for transforming raw issue descriptions into communicative, compliance-aware Linear tickets designed for fintech product managers working on tax and compliance products. Transform messy issue descriptions into professional, communicative Linear tickets for Nigerian fintech product managers working on tax compliance products. Automatically detects Nigerian tax implications (NTA 2025), FIRS requirements, company income tax (CIT), personal income tax (PIT), VAT, stamp duties, and compliance deadlines. Use this skill whenever converting Slack threads, emails, meeting notes, or support tickets to Linear tickets for Nigeria-focused tax/compliance work. Built with expert knowledge of the Nigeria Tax Act 2025, FIRS procedures, and Nigerian tax deadlines."
---
 
Fintech Ticket Generator
 
Overview
This skill helps you convert informal issue input (Slack threads, email chains, meeting notes, support requests, bugs identified/features needed/gaps unimplemented from codebase review) into polished Linear tickets that:
 
Clearly communicate the problem and solution to engineering teams
Identify compliance/regulatory implications specific to fintech
Estimate effort and prioritization with fintech context
Define measurable acceptance criteria that catch edge cases
Surface risk factors related to tax, regulatory, and data handling
 
The skill understands fintech terminology (VLT specs, tax withholding thresholds, regulatory reporting windows, KYC/AML, etc.) and incorporates domain knowledge into ticket generation.
 
Workflow
Step 1: Input Recognition & Parsing
Accept issue input in ANY format:
 
Slack messages: Single message or thread
Email threads: Raw email text or chains
Meeting notes: Unstructured notes, action items
Support tickets: Customer-reported bugs or feature requests
Chat transcripts: Problem discussions or brainstorms
Codebase review: Your analysis from reviewing a workspace folder
 
Parse the input to extract:
 
Core problem: What's the issue or feature request?
Context: Who reported it? What triggered it?
Constraints: Budget, timeline, dependencies?
Regulatory/compliance signals: Any mentions of tax, reporting, audits, compliance, KYC, AML, data retention, PII?
 
Step 2: Fintech Domain Assessment
Flag potential fintech/compliance considerations:
Tax & Compliance Keywords to look for:
 
Tax withholding, VAT/WHT, CIT
Regulatory filing deadlines based on NTA 2025
Account verification (KYC, AML, sanctions screening)
Data retention windows (7-10 year IRS requirements)
Currency/forex reporting thresholds
Audit trail and immutability requirements
PII and sensitive financial data handling
 
Risk Signals:
 
"Customer can't file their taxes"
"Failed compliance audit"
"Regulatory deadline approaching"
"Customer dispute / appeal process"
"Multi-currency or cross-border"
"Report discrepancy with tax authority"
 
If compliance signals present → flag as "High Compliance Impact" in the ticket.
Step 3: Structure the Ticket
Create a Linear ticket with the following structure:
Title
 
Concise, action-oriented (5-10 words)
Format: [Feature/Bug/Enhancement] Verb + Object
Examples:
 
"Add 1099-NEC withholding calculation validation"
"Fix VAT rounding error in tax reports"
"Implement SCA authentication for account linking"
 
 
 
Description
Use the following template:
## Problem Statement
[1-2 sentences: What's broken or missing? Why does it matter?]
 
## Current State
[How does it currently work, or what's missing?]
 
## Desired State
[What should happen instead? Include fintech context if applicable]
 
## Business Impact
[Why is this important? Customer pain? Compliance risk? Revenue impact?]
 
## Compliance & Regulatory Context
[If applicable: Tax implications, reporting requirements, audit concerns, timeline pressure]
[If not applicable: "N/A"]
 
## Technical Considerations
[Dependencies, integrations, data model changes, backwards compatibility concerns]
 
## Acceptance Criteria
- [ ] [Specific, testable criterion 1]
- [ ] [Specific, testable criterion 2]
- [ ] [Specific, testable criterion 3]
- [ ] Edge case handling: [describe]
- [ ] [If compliance-related] Audit trail logs what/who/when changes were made
- [ ] [If PII/financial data involved] Encrypted at rest; access logged
## Potential Risks & Mitigations
- **Risk**: [Risk 1] → **Mitigation**: [How to mitigate]
- **Risk**: [Risk 2] → **Mitigation**: [How to mitigate]
## Related Tickets / Dependencies
- [Link related Linear tickets or blockers]
## Success Metrics
- [How will we know this is done and working?]
Metadata Fields
Set on the Linear ticket:
Priority:
 
Urgent: Compliance deadline, customer escalation, regulatory block
High: Core feature, significant user impact, enables other work
Medium: Nice-to-have, incremental improvement
Low: Polish, future consideration
 
 
Estimate (use Linear's point system):
 
1 point: < 4 hours (simple bug fix, config change)
2 points: 4-8 hours (minor feature, isolated change)
3 points: 1 day (feature with tests, some integration)
5 points: 2-3 days (moderate feature, multiple components)
8 points: 3-5 days (complex feature, cross-team coordination)
13 points: 1-2 weeks (major feature, significant refactor)
 
Adjust upward if compliance/regulatory review required.
Labels (create/apply as needed):
 
compliance-critical (must pass audit, regulatory deadline)
tax-related (tax calculation, reporting, withholding)
pii-handling (touches customer data, PII, financial info)
fintech-risk (high fintech domain complexity)
customer-escalation (customer-reported, blocking workflow)
audit-trail-required (changes must be logged)
 
 
 
 
Example: Converting a Slack Thread
Input (Slack thread):
Customer Support: "We have a customer saying they can't see their 1099-NEC box 
amounts broken down on the annual summary. They're a contractor and need to reconcile 
it with what they report to the IRS. This is blocking them from filing."
 
PM: "How many customers affected?"
 
Support: "Just 1 for now, but this is a common request. Could be 20-30% of our 
self-employed users who file 1099."
 
Eng Lead: "Which boxes are missing?"
 
Support: "Boxes 1-3 are totaled, but we're not showing the breakdown by payer/month 
which some accountants want to verify."
Output (Linear Ticket):
Title: Enhance 1099-NEC summary to show payer/month breakdown
Description:
## Problem Statement
Contractors and self-employed users cannot see 1099-NEC income broken down by payer 
and payment month on their annual tax summary, blocking tax filing reconciliation.
 
## Current State
The annual summary shows total NEC amounts for boxes 1-3, but does not provide 
month-by-month or payer-specific granularity.
 
## Desired State
Annual summary displays a detailed table with 1099-NEC boxes 1-3 broken down by 
payer name and payment month, allowing users to cross-reference individual payments 
with IRS reporting forms.
 
## Business Impact
- Unblocks contractor tax filing workflow
- Improves trust in platform (users can audit our math)
- Potential 20-30% of self-employed user base affected
- Reduces support escalations
## Compliance & Regulatory Context
IRS Form 1099-NEC reporting deadline: Jan 31 (federal), varies by state.
Users must reconcile reported income; breakdown helps them verify accuracy.
This is a customer reconciliation tool, not IRS reporting—still improves compliance confidence.
 
## Technical Considerations
- Requires querying NEC transactions by date range and payer
- May need to enhance 1099 data model if payer relationship isn't tracked
- Could be added as a collapsible section or separate view on annual summary
- No PII/security changes required; display of user's own data
## Acceptance Criteria
- [ ] Annual summary includes expandable "1099-NEC Breakdown by Payer" section
- [ ] Breakdown shows: Payer Name | Month | Box 1 | Box 2 | Box 3 | Box 5c amounts
- [ ] Totals in breakdown match summary totals (pixel-perfect reconciliation)
- [ ] Handles multi-year view (show previous tax years if data available)
- [ ] Edge case: Users with 20+ payers show pagination or filter UI
- [ ] Print/export includes breakdown table
- [ ] Tested with real contractor accounts (QA data + anonymized prod data)
## Potential Risks & Mitigations
- **Risk**: Customers spot discrepancies in our 1099 calculations
  → **Mitigation**: QA carefully against source transactions; add audit footnote
- **Risk**: Users misuse breakdown for non-IRS reporting
  → **Mitigation**: Add disclaimer "For personal reference only; IRS Form 1099-NEC is official record"
## Success Metrics
- Customers can export/screenshot breakdown for tax filing
- Support ticket volume for "I can't see my 1099 details" drops by 80%
Tips for Effective Tickets
 
Compliance Signals: Always flag tax/regulatory context—it affects prioritization & timelines.
Acceptance Criteria are Golden: Be specific. "Works correctly" is not testable. "All values round to nearest cent using ROUND_HALF_UP" is.
Edge Cases Matter: In fintech, edge cases = regulatory risk. Call them out explicitly.
Estimates are Guidance: Adjust upward if compliance review, audit logging, or cross-team coordination is needed.
Link Related Work: If this unblocks another ticket or depends on one, link it. Linear's linking is powerful.
Avoid Jargon Overload: Explain fintech terms if the ticket might reach non-domain eng (product, design).
 
 
When to Use This Skill
 
Converting Slack threads to Linear tickets
Drafting tickets from customer support escalations
Turning meeting action items into structured work
Parsing email chains and creating track items
Reviewing ticket drafts for clarity & completeness
Assessing fintech compliance impact of requested changes
 
This skill is designed for Nigerian fintech product managers and focuses EXCLUSIVELY 
on Nigerian tax law (NTA 2025), FIRS procedures, and Nigeria-specific tax compliance.
 
Key Features:
✅ NTA 2025 section awareness (all chapters and parts)
✅ FIRS compliance & deadline detection
✅ Nigerian tax types: CIT, PIT, VAT, Withholding Tax, Stamp Duties
✅ 6-year record retention requirement per NTA 2025 Section 208
✅ Audit trail & compliance tracking
✅ Petroleum operations & royalty calculations
✅ Transfer pricing & non-resident taxation
✅ Professional Linear ticket generation with compliance context
 
What This Skill Does NOT Do
 
Create Linear accounts or authenticate to Linear API (you handle that)
Automatically post tickets to Linear (you copy-paste or use Linear's import)
Replace human judgment on prioritization (it provides structure & guidance)
Handle non-fintech domains (it's tuned for tax/compliance products)