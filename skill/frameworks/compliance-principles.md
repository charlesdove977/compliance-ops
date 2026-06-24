<compliance_principles>
## Purpose
The framework-agnostic core. These principles hold across HIPAA, SOC2, GDPR, PCI, and any other regime: the specifics change, the architecture logic does not. Load this for every Compliance Ops engagement; load the regime-specific framework (`hipaa.md`, `soc2.md`) on top of it.

---

## Principle 1 — "Local" describes the files, not the data the AI processes

Claude Code runs locally, but the model is a **remote brain**. Every time the agent reads a file, the contents are transmitted to the model endpoint for inference. So "the folder is on my computer" does NOT mean protected data stays local. What matters is **what you hand the AI**, not where the files sit.

**Consequence:** the compliance lever is the *input* to the AI, not the *location* of the project.

---

## Principle 2 — The trigger is TOUCHING the data, not STORING it

Every regime defines a protected data class (PHI for HIPAA, "customer data" for SOC2, personal data for GDPR). A vendor falls in scope the instant it **creates, receives, maintains, or transmits** that data — even for milliseconds, even without saving it.

**"We don't store it, it just passes through" does NOT exempt a system.** Receiving and forwarding still counts. This is the single most common compliance self-deception. A server that sees a patient's name for 50ms and forwards it has received protected data.

---

## Principle 3 — The "conduit exception" is narrow and does NOT cover your app

HIPAA's conduit exception (and its equivalents) covers dumb transmission pipes only — ISPs, telcos, the postal service. A web server, a serverless function, an automation tool, or an AI model that *ingests and processes* the data is not a conduit. If your app touches protected data, it is in scope. Do not rely on "it's just passing through."

---

## Principle 4 — Two lanes: separate protected data from everything else

The cleanest compliant architecture splits the build into two lanes:

- **Lane 1 — No protected data.** Marketing site, content, code, SOPs, internal docs, non-regulated business lines. The AI works here freely. No special agreement needed.
- **Lane 2 — Protected data.** Intake, records, regulated workflows. Lives ONLY inside vendors that have signed the right agreement. The AI never enters this lane unless it is on a covered endpoint.

Most of what a builder does lives in Lane 1. Keep it there.

---

## Principle 5 — Shrink the protected-data footprint to the fewest covered vendors

Every system you let touch protected data is another agreement to sign, pay for, and audit. The win is NOT "make everything compliant" — it is "route protected data through as few covered systems as possible, and keep everything else out of scope." Fewer vendors in scope = cheaper, simpler, more defensible.

---

## Principle 6 — Keep the AI out of the protected-data path (unless explicitly covered)

Default: the AI **builds and manages everything around** the protected data without ever touching it. It designs the intake form, configures the CRM, writes the automations — but the live protected data flows through covered vendors, not through the AI.

Only put the AI *in* the protected-data path when there is a real need (e.g. summarizing records), and then only on a covered endpoint (see `hipaa.md` for the AI-on-PHI path). Adding the AI to the protected lane is a deliberate, documented upgrade — never an accident.

---

## Principle 7 — A vendor agreement covers the vendor's slice, not your whole system

A BAA / DPA makes the *vendor* a compliant handler of protected data on their infrastructure. It does NOT make your application, your access controls, your local environment, or your data-handling practices compliant. Those remain the builder's responsibility: access control, encryption at rest, minimum-necessary, audit logging, and agreements with every other vendor in the chain.

---

## The Compliance Ops decision flow

```
1. What is being built?                → scope the surfaces
2. Will any surface touch protected data?
       NO  → Lane 1. Build freely. Done. (note it in the data-flow map)
       YES → continue
3. Can that surface be kept out of the protected path?
       YES → redesign so protected data flows elsewhere (native covered form, etc.)
       NO  → that vendor MUST be under a signed agreement (check vendor-matrix.md)
4. Does the AI need to read the protected data?
       NO  → keep AI in Lane 1. (default, preferred)
       YES → AI must run on a covered endpoint (Bedrock+BAA / API+BAA+ZDR)
5. Document the data-flow map + vendor agreement checklist.
6. State the disclaimer. Hand to the compliance officer.
```

## Anti-patterns

- **"It's local so it's fine."** Wrong — see Principle 1. The AI transmits what it reads.
- **"We don't store it."** Wrong — see Principle 2. Touching triggers scope.
- **"The hosting is in AWS so it's HIPAA."** Hosting the files ≠ covering the inference / data path. The covered agreement has to cover the surface that actually touches the data.
- **"This skill makes Claude compliant."** It does not. It keeps protected data away from Claude and documents the boundary.
</compliance_principles>
