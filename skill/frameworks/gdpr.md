<gdpr_framework>
## Purpose
The GDPR-specific framework: what GDPR protects, the controller/processor roles, the obligations a builder must design for, how to vet vendors (DPA + transfer mechanism), where the AI fits, and the architecture patterns. Built on top of `compliance-principles.md`. Pair with `vendor-matrix.md`.

Verified against official EU sources and vendor DPA pages (2025–2026). Re-verify before relying on it; transfer frameworks and vendor status change.

---

## What GDPR protects

**Personal data** (Art. 4) — any information relating to an identified or identifiable natural person ("data subject"): name, ID number, location, online identifiers. **IP addresses and cookie IDs are personal data.**

**Special category data** (Art. 9) — race/ethnicity, political opinions, religion, trade-union membership, genetic/biometric data, health, sex life, orientation. Processing is **prohibited by default** — generally needs **explicit consent** or another Art. 9(2) exception.

**Territorial scope** (Art. 3) — applies to (a) any controller/processor established in the EU/EEA, and (b) anyone outside the EU who offers goods/services to, or monitors the behavior of, people in the EU. **A US company with no EU office is still in scope if it serves or tracks EU residents.**

**Roles:**
- **Controller** — decides the *purposes and means* (the "why/how"). Primary accountability.
- **Processor** — processes on the controller's documented instructions (your host, email sender, AI vendor).

---

## Core obligations a builder must design for

- **Lawful basis** (Art. 6) — every processing activity needs one of six: consent, contract, legal obligation, vital interests, public task, legitimate interests. Pick + document it before building.
- **Core principles** (Art. 5) — **data minimization** (collect only what you need), **purpose limitation**, **storage limitation** (retention + deletion), accuracy, integrity/confidentiality, accountability.
- **Data subject rights** (Arts. 15–22) — access, rectification, **erasure ("right to be forgotten")**, restriction, **portability** (machine-readable export), objection. Your system needs real **export** and **delete** mechanisms that cascade to processors.
- **DPA requirement** (Art. 28) — you may only use processors under a **binding written DPA**; the processor must process only on instructions, apply Art. 32 security, assist with data-subject requests + breach notice, delete/return data at end of service, and flow the same terms to **sub-processors**. Signed DPA with **every** processor + sub-processor.
- **Breach notification** (Art. 33/34) — notify the supervisory authority **within 72 hours** of awareness where feasible; notify individuals if high risk.
- **International transfers** (Ch. V) — sending EU personal data outside the EEA needs a mechanism: **adequacy decision** (Art. 45), **Standard Contractual Clauses / SCCs** (Art. 46; Module 2 = controller→processor, Module 3 = processor→processor), or the **EU-US Data Privacy Framework (DPF)** for self-certified US orgs.

> **Transfer status (verify at build time):** the EU General Court upheld the DPF on 3 Sept 2025; a CJEU appeal is pending. Treat DPF as valid-but-watch; **SCCs are the durable fallback.** Schrems II caveat: a US-incorporated provider is reachable under the US CLOUD Act regardless of server location — an EU region addresses storage, not the US-access concern by itself.

---

## The vendor angle — DPA + transfer coverage

You (controller) need a **signed DPA** with every processor, and each needs a valid **transfer mechanism** (DPF and/or SCCs). See `vendor-matrix.md` for the full table.

Headlines: AWS, Google Cloud, Vercel, Zapier, Cloudflare, GoHighLevel offer a GDPR DPA and are DPF-certified + SCC-backed. Make is EU-headquartered. **Self-hosted n8n** lets you pin data to an EU region — but then *you* own deletion + retention (set `EXECUTIONS_DATA_MAX_AGE`). Anthropic: see below.

---

## Where the AI fits

Keep EU personal data out of the AI's path **unless the model vendor is under a signed DPA with proper transfer safeguards**. Sending EU personal data to a US AI endpoint **is a "transfer"** needing SCCs/DPF.

**Anthropic specifics (confirmed):**
- DPA **auto-incorporates** into the Commercial Terms — accepting the Commercial ToS accepts the DPA (effective 24 Feb 2025).
- For the **Claude API and Claude for Work (Team/Enterprise)**, the customer is **controller** and Anthropic is **processor**, processing only on instructions; no training on this data by default.
- Transfer posture: SCCs **Module 2 + Module 3**, UK IDTA, Swiss addendum.
- **Consumer tiers (Free, Pro, Max, Claude Code consumer accounts) are on Consumer Terms with NO DPA** — never route EU personal data through them in a product. Use the commercial API / Claude for Work.

---

## Architecture patterns + decision flow

```
1. Map personal data — fields, from whom, where stored, who it flows to. Flag Art. 9 special-category.
2. Establish lawful basis per activity (Art. 6) → record it.
3. Minimize — drop unneeded fields; pseudonymize/anonymize where possible.
4. Consent capture where consent is the basis (and for special-category / non-essential cookies) — granular, opt-in, withdrawable, logged.
5. DPA chain — every processor/sub-processor has a signed DPA + transfer mechanism. Keep a sub-processor list.
6. Data-subject rights — build export (portability) + delete (erasure) that cascade to processors/backups.
7. Region/transfer — prefer EU regions; remember US-parent CLOUD Act exposure; pick AI/vendor tiers that carry a DPA.
8. Breach readiness — logging + detection + a runbook to hit the 72-hour window.
```

Reusable: EU-region pinning; pseudonymize-at-ingest; **redact/tokenize PII before it reaches the AI**; per-record retention TTL + automated pruning; a committed "data map + DPA register" artifact.

---

## Anti-patterns

1. **No lawful basis** — processing personal data with no documented Art. 6 basis.
2. **No DPA with a processor** — piping EU personal data into a SaaS/AI vendor without a signed Art. 28 DPA + sub-processor coverage.
3. **US transfer with no SCCs/DPF** — calling a US endpoint with EU personal data and no transfer mechanism (route AI through a DPA'd commercial tier, not consumer Claude).
4. **Indefinite retention** — no storage limitation / TTL; logs kept forever (classic n8n/Zapier trap).
5. **No deletion/export mechanism** — can't fulfill erasure/portability because deletion doesn't cascade.
6. **Special-category data on a weak basis** — health/biometric processed without explicit consent.

---

## Honest framing
A build-time guardrail helps you **build with GDPR principles in mind and document your data-flow + DPA chain** (data map, lawful-basis register, sub-processor list, transfer mechanisms). It does **NOT** make you GDPR compliant and is **not legal advice** — a DPO or qualified counsel validates lawful bases, DPAs/SCCs, and transfer assessments. (See `disclaimer.md`.)

Sources: [GDPR Art. 4](https://gdpr-info.eu/art-4-gdpr/) · [Art. 28 (DPA)](https://gdpr-info.eu/art-28-gdpr/) · [Art. 44–49 (transfers)](https://gdpr-info.eu/chapter-5/) · [Anthropic DPA](https://privacy.claude.com/en/articles/7996862-how-do-i-view-and-sign-your-data-processing-addendum-dpa) · [EU-US DPF](https://www.dataprivacyframework.gov/Program-Overview)
</gdpr_framework>
