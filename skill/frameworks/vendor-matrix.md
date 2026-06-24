<vendor_matrix>
## Purpose
The per-vendor, multi-regime lookup: which common build / automation / hosting / payment vendors carry the right coverage for each compliance regime, and what that means for routing protected data. This is the table Compliance Ops checks before letting any vendor into the protected-data path (Lane 2).

**The agreement you need depends on the regime:**
- **HIPAA** → a signed **BAA** (Business Associate Agreement)
- **SOC 2** → the vendor's own **SOC 2 Type II** report + a **DPA**
- **GDPR** → a signed **DPA** + a valid **transfer mechanism** (SCCs and/or EU-US DPF)
- **PCI-DSS** → a **PCI DSS Level 1** payment processor whose **hosted fields / tokenization** keep card data out of your environment

**Verify before relying on.** Vendor policies, prices, and attestations change. When a build depends on it, confirm on the vendor's current trust/compliance page. Last verified 2026-06.

---

## Cloud / hosting / infra

| Vendor | HIPAA BAA | SOC 2 Type II | GDPR DPA (+ transfer) | Notes |
|---|---|---|---|---|
| **AWS** | ✅ (AWS Artifact) | ✅ | ✅ DPA + DPF + SCCs | 160+ HIPAA-eligible services; EU regions available |
| **AWS Bedrock** | ✅ (under AWS BAA) | ✅ | ✅ | The compliant AI-on-PHI route; Claude runs here |
| **Google Cloud / Vertex** | ✅ | ✅ | ✅ DPA + DPF + SCCs | Claude on Vertex under Google's BAA/DPA |
| **Vercel** | ✅ (Pro add-on / Ent) | ✅ | ✅ DPA + DPF + SCCs | BAA on Pro tier; DPF-certified |
| **Railway** | ✅ (Enterprise, ~$1k/mo min) | ✅ (SOC 2 + SOC 3) | ✅ DPA (verify DPF) | HIPAA gated to Enterprise; DPA self-serve |
| **Cloudflare** | varies (Ent) | ✅ | ✅ DPA + DPF + SCCs | Data Localization Suite keeps EU traffic/metadata in EU |
| **Supabase** | ✅ (Team/Ent) | ✅ | ✅ DPA + SCCs | Pick an EU region; US-incorporated → CLOUD Act applies |
| **MongoDB Atlas** | ✅ | ✅ (+ PCI DSS, ISO 27001) | ✅ DPA | Mongo as processor |

## AI / model endpoints

| Vendor | HIPAA | SOC 2 | GDPR | Notes |
|---|---|---|---|---|
| **Anthropic API** (commercial) | ✅ BAA (sales; 30-day retention) | ✅ Type I & II | ✅ DPA auto-incorporated, SCCs Mod 2+3 | Customer = controller, Anthropic = processor; no training by default |
| **Claude Code** via API key + ZDR | ✅ | ✅ | ✅ | CLI only; ZDR required for HIPAA |
| **Claude via Bedrock / Vertex** | ✅ | ✅ | ✅ | Under the cloud provider's BAA/DPA |
| **Claude Pro / Max / Team (consumer)** | ❌ | ❌ for product data | ❌ no DPA | NEVER route protected data through consumer tiers |

## CRM / automation

| Vendor | HIPAA BAA | SOC 2 Type II | GDPR DPA | Notes |
|---|---|---|---|---|
| **GoHighLevel** | ✅ (HIPAA add-on ~$297/mo) | ✅ (Security/Confidentiality/Availability) | ✅ DPA + DPF | Not compliant by default; enable per sub-account |
| **Zapier** | ❌ no BAA | ✅ (+ SOC 3) | ✅ DPA + DPF + SCCs | SOC2/GDPR OK, but **never for PHI** (no BAA) |
| **Make (Integromat)** | ❌ no BAA | ✅ (+ SOC 3, ISO 27001) | ✅ DPA (EU-HQ) | Same: fine for SOC2/GDPR data, **not PHI** |
| **n8n (cloud)** | ❌ native | ✅ (Enterprise report only) | ✅ DPA + SCCs | Self-hosted on your infra has NO vendor attestation |
| **n8n (self-hosted)** | viable (your infra + cloud BAA) | burden on you | EU-region pin + your controls | You own deletion/retention (`EXECUTIONS_DATA_MAX_AGE`) |
| **Keragon** | ✅ (HIPAA automation) | — | — | Managed HIPAA automation; done-for-you vs self-host n8n |

## EHR (HIPAA)

| Vendor | BAA | Notes |
|---|---|---|
| **TherapyNotes** | ✅ signs BAA | **No official public API** — automating into it relies on unofficial bridges (risk) |

## Payment processors (PCI-DSS)

| Processor | PCI Level | Keeps card data out of your env via | Target SAQ |
|---|---|---|---|
| **Stripe** | L1 Service Provider | Checkout (hosted redirect) / Elements (hosted fields) | SAQ A (redirect) / A-EP (in-page) |
| **Braintree (PayPal)** | L1 | Hosted Fields, Drop-in | SAQ A |
| **Adyen** | L1 | Drop-in, Components, hosted checkout | SAQ A |
| **Square** | L1 | Web Payments SDK (`card.tokenize()`) | SAQ A |

---

## How to use this table

1. **Identify the regime(s)** from the interview (HIPAA / SOC2 / GDPR / PCI — can be more than one).
2. **Any vendor in Lane 2 (touches protected data) must have the right coverage for that regime.** A ✅ in the HIPAA column ≠ a ✅ for PHI if you actually need SOC2/GDPR too — check the relevant column.
3. **A "❌ no BAA" tool (Zapier, Make) can still be fine for SOC2/GDPR customer data** (they hold SOC 2 + DPA) — but is **never** allowed for **PHI**. Match the column to the regime.
4. **PCI is different:** there is no "agreement" to sign for your data — the move is to never touch card data at all (processor hosted fields → SAQ A). The AI stays entirely out of the card data environment.
5. **Prefer the cheapest covered tier for the builder's stage.** BAA-on-Pro (Vercel, GoHighLevel) over an Enterprise commit (Railway $1k/mo) unless required.
6. **The host can stay out of Lane 2** if a native covered form / hosted payment field carries the protected data — then the host needs no agreement at all (cheapest).
7. **Coverage is the vendor's slice only.** You still own access control, encryption at rest, minimum-necessary/minimization, audit logging, and the agreement chain.

## Routing heuristics

- **PHI intake** → native form of a BAA-covered CRM (GoHighLevel HIPAA). Never Zapier/Make.
- **Customer-data (SOC2/GDPR) automation** → Zapier/Make/n8n are OK *with a DPA*; document them as sub-processors.
- **EU personal data** → prefer EU regions; ensure each vendor has a DPA + SCCs/DPF; redact PII before it reaches the AI.
- **Payments** → processor hosted fields/redirect (Stripe Checkout, Braintree Drop-in); store token + last4 only; AI never sees PAN.
- **AI must read protected data** → commercial Claude endpoint under BAA/DPA (API+BAA+ZDR for PHI, or Bedrock/Vertex). Never consumer tiers.

*Not legal advice. Confirm vendor terms + your architecture with a compliance officer / counsel before protected data flows.*
</vendor_matrix>
