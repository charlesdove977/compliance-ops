<soc2_framework>
## Purpose
The SOC 2-specific framework: what SOC 2 is, the in-scope data class, how to vet vendors (SOC 2 Type II + DPA), where the AI fits, and the controls a builder bakes in. Built on top of `compliance-principles.md`. Pair with `vendor-matrix.md`.

Verified against AICPA and vendor trust pages (2025–2026). Re-verify before relying on it; attestations and report scopes change.

---

## What SOC 2 is

**An AICPA attestation, not a law.** SOC 2 is a System and Organization Controls report defined under the AICPA's **2017 Trust Services Criteria (revised points of focus, 2022)** — the current standard. There is no government "SOC 2 certificate"; an independent CPA firm issues an attestation report.

**Five Trust Services Criteria (TSC):**
1. **Security (Common Criteria / CC)** — the ONLY mandatory category; every SOC 2 audit includes it.
2. **Availability** — uptime, DR, capacity (optional).
3. **Processing Integrity** — complete, valid, accurate, timely, authorized processing (optional).
4. **Confidentiality** — protection of confidential business data / IP (optional).
5. **Privacy** — personal information handled per commitments (optional).

**Type I vs Type II:** Type I = control *design* at a point in time. Type II = design **and operating effectiveness** over a period (typically 6–12 months). Enterprise buyers want **Type II**.

**Who needs it:** SaaS / service orgs handling customer data. In practice it is an **enterprise procurement gate** — large buyers won't sign without a current Type II report.

---

## The in-scope data class (how the trigger differs from HIPAA)

HIPAA triggers on a *specific data type* (PHI). **SOC 2 has no statutory data type** — it is about whether your controls operate effectively against your stated commitments.

**For guardrail purposes, treat the in-scope class as: customer data + every system that stores, processes, transmits, or logs it.** Production DBs, app servers, the auth layer, observability/logs, secrets, and every vendor in the data path. The trigger is "does this touch customer data or a system inside the SOC 2 boundary," not "is this a regulated type."

---

## The vendor angle — the two-part check

SOC 2's equivalent of HIPAA's BAA is a **per-vendor two-part check**: (a) does the sub-processor hold its **own SOC 2 Type II**, and (b) does it offer a **DPA (Data Processing Agreement)**? Both true + DPA executed = a defensible link in your sub-processor chain.

See `vendor-matrix.md` for the full table. Headlines (all verified to hold SOC 2 Type II + offer a DPA): AWS, Google Cloud, Vercel, Railway, GoHighLevel, Zapier, Make, Anthropic, Supabase, Cloudflare, MongoDB Atlas. **n8n: only n8n Cloud Enterprise carries the report — self-hosted n8n has NO vendor attestation, the control burden moves to you.**

**Caveats:** most vendors gate the report behind NDA / a trust portal — "they're SOC 2" is not "you've received and reviewed the report." And a vendor's SOC 2 covers *their* controls, not yours (shared responsibility).

---

## Where the AI fits

Same principle as HIPAA: keep customer data out of the model's path **unless the endpoint is a vendor with SOC 2 + an executed DPA**.

**Anthropic qualifies.** Anthropic holds **SOC 2 Type I & Type II, ISO 27001:2022, ISO/IEC 42001:2023**, and a HIPAA-ready configuration. Its **DPA with SCCs is automatically incorporated into the Commercial Terms of Service**, and for the **API / Claude for Work** the customer is controller and Anthropic is processor (no training on your data by default).

**Covered endpoints for customer data → Claude:** Anthropic API (Commercial Terms + DPA), AWS Bedrock (under AWS's SOC 2/DPA), or Google Vertex (under Google Cloud's). **Consumer Claude.ai / Pro / Max is NOT the commercial path — keep it out of a SOC 2 data flow.**

So unlike the strict "keep AI out" default, SOC 2 *does* let the AI process customer data on a commercial Claude endpoint under a DPA — but document it as a sub-processor and keep consumer tiers out.

---

## The six controls a builder bakes in (maps to Common Criteria)

1. **Data inventory** — enumerate where customer data lives, flows, and is logged. Step zero; you can't protect or scope what you haven't mapped.
2. **Least-privilege access** — scoped IAM, no shared admin creds, MFA, time-boxed access. (CC6)
3. **Encryption in transit + at rest** — TLS everywhere; managed DB/bucket encryption; secrets in a vault, never committed to git. (CC6)
4. **Audit logging** — immutable, centralized logs of access + changes to customer data, retained and reviewable. (CC7)
5. **Vendor DPA chain** — every sub-processor has its own SOC 2 Type II + executed DPA; maintain a sub-processor list. (CC9)
6. **Change management** — PR review, CI checks, no direct prod pushes. (CC8)

---

## Decision flow — "does this code/feature need SOC 2 controls?"

```
Touches customer data or a system in the SOC 2 boundary?
  NO  → standard hygiene; note out of scope.
  YES → 1. Data inventoried + classified?            → if no, STOP, inventory first.
        2. Least-privilege + encryption in/at rest?  → if no, add before merge.
        3. Access + changes audit-logged?            → if no, add logging.
        4. New vendor / AI endpoint?
             → has SOC 2 Type II + executed DPA?
                  YES → add to sub-processor list.
                  NO  → STOP. Don't route customer data there.
        5. Shipped via reviewed change-management?    → if no, fix process.
```

---

## Anti-patterns

1. **Treating vendor SOC 2 as your SOC 2.** "We're on AWS so we're SOC 2" is false — shared responsibility; your own controls are audited.
2. **Secrets in the repo / plaintext env.** Keys/creds committed to git or baked into client bundles — the most common CC6 finding.
3. **Self-hosting a tool only the vendor's cloud tier covers** (self-hosted n8n) — the attestation burden silently shifts to you.
4. **No audit logging until audit time.** Type II needs evidence *across a period*; logging enabled a week before produces no history.
5. **Routing customer data to a consumer AI endpoint** instead of a commercial API under a DPA — breaks the sub-processor chain.
6. **Shadow sub-processors** — adding a vendor mid-build with no DPA / no sub-processor-list entry.

---

## Honest framing
A build-time guardrail helps you **build with SOC 2 controls in mind and document the in-scope boundary**. It does **NOT** make you SOC 2 certified — only an independent CPA firm's **Type II report**, over a real observation period, attests that. Not legal or audit advice. (See `disclaimer.md`.)

Sources: [AICPA SOC suite](https://www.aicpa-cima.com/resources/landing/system-and-organization-controls-soc-suite-of-services) · [Anthropic certifications](https://privacy.claude.com/en/articles/10015870-what-certifications-has-anthropic-obtained) · [AWS SOC FAQs](https://aws.amazon.com/compliance/soc-faqs/) · [Cloudflare SOC 2](https://www.cloudflare.com/trust-hub/compliance-resources/soc-2/)
</soc2_framework>
