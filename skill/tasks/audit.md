<purpose>
Audit an existing build, system, or data flow for compliance gaps — find where protected data touches an uncovered vendor or the AI, and produce a prioritized remediation list.
</purpose>

<user-story>
As someone who already built a system, I want to know where my protected data is exposed to vendors without the right agreement, so that I can close the gaps before an audit or a breach.
</user-story>

<when-to-use>
- User runs `/compliance-ops audit`.
- User asks "is what I already built compliant?" or "where are my compliance gaps?".
- Before a real compliance review / audit.
</when-to-use>

<context>
@frameworks/compliance-principles.md
@frameworks/disclaimer.md
</context>

<references>
@frameworks/hipaa.md
@frameworks/soc2.md
@frameworks/vendor-matrix.md
@templates/data-flow-map.md
</references>

<steps>

### 1. Disclaimer, then scope the audit
State the short-form disclaimer. Ask which system/flow to audit and which regime applies.

### 2. Trace the actual data flow
Walk the real path the protected data takes, end to end: where it's collected → every system it passes through → where it lands. Inspect config/code where available (forms, webhooks, automation steps, integrations, any AI steps). Do not assume — trace.

### 3. Flag every touchpoint
For each system in the path, determine if it creates/receives/maintains/transmits protected data (Principle 2). For each that does, check `vendor-matrix.md`:
- Covered + right tier → OK.
- Covered but wrong tier / not enabled → **gap (fixable)**.
- Not covered (Zapier/Make/uncovered host) → **gap (critical)**.
- AI reading protected data on a non-covered endpoint → **gap (critical)**.

### 4. Prioritize remediation
Rank gaps: critical (protected data on an uncovered vendor / the AI) first, then fixable (wrong tier, agreement not signed). For each, give the specific fix (enable add-on, swap vendor, redesign to native covered form, move AI out of the path).

### 5. Produce the audit output
Summarize the traced flow, the gap list with severity, and the remediation steps. Offer to generate the full data-flow map (`/compliance-ops document`).

### 6. Close with the disclaimer.
</steps>

<output>
- The traced end-to-end data flow.
- A severity-ranked list of compliance gaps with specific fixes.
- Offer to generate full documentation.
</output>

<acceptance-criteria>
- [ ] Disclaimer at open and close.
- [ ] Actual flow traced (not assumed), config inspected where available.
- [ ] Every protected-data touchpoint checked against the vendor matrix.
- [ ] Gaps ranked by severity with a concrete fix each.
</acceptance-criteria>
