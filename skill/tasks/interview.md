<purpose>
The default Compliance Ops flow. Interview the user about what they are building and what compliance they care about, load the matching framework, then guard the build so protected data is designed out of the AI's path and routed only through covered vendors. Ends by offering to document the data-flow map.
</purpose>

<user-story>
As someone building in a regulated industry, I want the AI to understand my compliance constraints before it builds anything, so that what it builds is safe by design and I am not retrofitting compliance after a leak.
</user-story>

<when-to-use>
- Default route when `/compliance-ops` is invoked with no subcommand.
- Auto-fires when a message matches a trigger phrase (compliance, HIPAA, PHI, BAA, "is this safe to put patient data through", "build this HIPAA safe").
- Before building any intake form, CRM workflow, automation, or site that could touch protected data.
</when-to-use>

<context>
Read the framework-agnostic core before interviewing.
@frameworks/compliance-principles.md
@frameworks/disclaimer.md
</context>

<references>
Lazy-load the regime-specific framework once the concern is known:
@frameworks/hipaa.md (HIPAA / PHI / healthcare / behavioral health)
@frameworks/soc2.md (SOC 2 / customer data / enterprise procurement)
@frameworks/gdpr.md (GDPR / EU users / personal data)
@frameworks/pci-dss.md (PCI / payments / card data)
@frameworks/vendor-matrix.md (whenever evaluating a vendor for Lane 2 — multi-regime)
@checklists/phi-safety.md (run before the AI builds anything that could touch protected data — covers all regimes)
</references>

<steps>

### 1. Open with the disclaimer, then interview
State the short-form disclaimer up front (not legal advice; cannot make a consumer subscription handle protected data). Then ask the two core questions — one group, then wait:
1. **What are you building?** (website, intake form, CRM workflow, automation, full system…)
2. **What are you worried about / what regime applies?** (HIPAA/patient data, SOC 2/customer data, GDPR/EU users, PCI/payments, "not sure")

If the regime is unclear, infer from the industry/feature and confirm: behavioral health → HIPAA; SaaS with enterprise buyers → SOC 2; EU users / personal data → GDPR; anything taking card payments → PCI-DSS. More than one can apply at once.

### 2. Load the matching framework(s)
- HIPAA / PHI / healthcare → `frameworks/hipaa.md`.
- SOC 2 / customer data / enterprise procurement → `frameworks/soc2.md`.
- GDPR / EU personal data → `frameworks/gdpr.md`.
- PCI / payments / card data → `frameworks/pci-dss.md`.
- Load more than one if the build spans regimes (e.g. an EU healthcare SaaS taking payments = HIPAA + GDPR + PCI). Always layer on `compliance-principles.md`.

### 3. Map the surfaces and find the protected-data touchpoints
List every surface in what they're building (pages, forms, storage, automations, integrations, AI steps). For each, ask: **does this surface create, receive, maintain, or transmit protected data?** Use Principle 2 — touching counts, storing is not required.

Mark each surface **Lane 1 (no protected data)** or **Lane 2 (protected data)**.

### 4. Apply the decision flow (from compliance-principles.md)
For every Lane 2 surface:
- **Can it be kept out of the protected path?** If yes, redesign (e.g. native covered intake form so PHI bypasses the host). Prefer this.
- **If it must touch protected data,** it MUST be a covered vendor — check `vendor-matrix.md`. If the named tool is a "No" (Zapier/Make), flag it and propose a covered swap.
- **Does the AI need to read the protected data?** Default NO — keep AI in Lane 1. If genuinely yes, require a covered endpoint (Bedrock+BAA / API+BAA+ZDR) and say so explicitly.

### 5. Run the tripwire checklist before building
Before letting Claude build, run `checklists/phi-safety.md`. If any tripwire fires (AI about to read a protected-data file, a no-BAA tool in the protected path, protected data landing on an uncovered host), STOP and surface it plainly. Do not build through a fired tripwire.

### 6. State the safe-by-design plan
Summarize: which surfaces are Lane 1, which are Lane 2, which vendors need an agreement, and confirm the AI is out of the protected path. Then build (or hand off the build) along that plan.

### 7. Offer documentation
Offer to run `/compliance-ops document` to produce the data-flow map + vendor BAA checklist for their compliance officer.

### 8. Close with the disclaimer
Always end with the short-form disclaimer.
</steps>

<output>
- A surface-by-surface Lane 1 / Lane 2 classification of what they're building.
- A safe-by-design build plan: covered vendors for Lane 2, AI kept out of the protected path.
- Any fired tripwires surfaced and resolved before building.
- An offer to generate the compliance documentation.
</output>

<acceptance-criteria>
- [ ] Disclaimer stated at open and close.
- [ ] Both interview questions asked and answered before building.
- [ ] Every surface classified Lane 1 or Lane 2.
- [ ] Every Lane 2 vendor checked against `vendor-matrix.md`; no-BAA tools flagged.
- [ ] AI confirmed out of the protected-data path (or a covered endpoint explicitly required).
- [ ] Tripwire checklist run; no build proceeded through a fired tripwire.
</acceptance-criteria>
