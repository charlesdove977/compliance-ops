<vendor_baa_matrix>
## Purpose
The per-vendor lookup: which common build/automation/hosting vendors will sign a BAA, on which tier, and what that means for routing protected data. This is the table Compliance Ops checks before letting any vendor into the protected-data path (Lane 2).

**Verify before relying on.** Vendor policies and prices change. When a build depends on it, confirm on the vendor's current trust/compliance page or via WebSearch. Last verified 2026-06.

---

## HIPAA BAA availability — common build stack

| Vendor | Signs a BAA? | Tier / condition | Notes |
|---|---|---|---|
| **GoHighLevel** | Yes | HIPAA add-on (~$297/mo), BAA included | Not compliant by default; enable per sub-account; irreversible once on |
| **Vercel** | Yes | **BAA add-on on Pro** (dashboard) or Enterprise | Affordable entry; covers global infra |
| **Railway** | Yes | **Enterprise only**, ~$1,000/mo minimum spend | No BAA below that threshold |
| **AWS** | Yes | Self-service BAA via **AWS Artifact** | 160+ HIPAA-eligible services; only eligible services may touch PHI |
| **AWS Bedrock** | Yes (under AWS BAA) | HIPAA-eligible; Claude runs here | The compliant AI-on-PHI route (Path B in `hipaa.md`) |
| **Google Cloud / Vertex AI** | Yes | Under Google Cloud BAA | Claude available via Vertex |
| **Anthropic API** | Yes | Commercial BAA via sales; 30-day retention | Consumer Pro/Max/Team NOT eligible |
| **Anthropic Claude Code** | Yes | API key on BAA account + **ZDR enabled** | Subscription login NOT eligible |
| **Zapier** | **No** | None | Does not sign BAAs on any plan — keep PHI out |
| **Make (Integromat)** | **No** | None | No HIPAA program as of early 2026 — keep PHI out |
| **n8n (cloud)** | No native BAA | — | Not compliant out of the box |
| **n8n (self-hosted)** | Viable | Run on AWS under AWS BAA + your safeguards | PHI stays on your infra; compliance burden is on you as operator |
| **TherapyNotes (EHR)** | Yes | Signs a BAA | **No official public API** — automating into it relies on unofficial bridges (risk) |
| **Keragon** | Yes | Managed HIPAA automation platform | The done-for-you (pricier) alternative to self-hosting n8n |

---

## How to use this table

1. **Any vendor in Lane 2 (touches protected data) must have a "Yes" and be on the right tier.** If it's a "No" (Zapier, Make), it cannot carry protected data — redesign so it doesn't, or swap it.
2. **Prefer the cheapest covered tier for the builder's stage.** A pre-revenue clinic uses BAA-on-Pro vendors (Vercel Pro, GoHighLevel add-on), not an Enterprise commit (Railway $1k/mo) they don't need yet.
3. **The host can stay out of Lane 2.** If the marketing host never receives protected data (native covered intake form pattern), it needs no BAA at all — even if it's Railway/Vercel. Cheapest of all.
4. **A "Yes" covers the vendor's slice only.** The builder still owns access control, encryption at rest, minimum-necessary, audit logging (Principle 7).

---

## Routing heuristics

- **Intake/forms touching PHI** → native form of the covered CRM (GoHighLevel HIPAA), not a custom form on an uncovered host.
- **Automations touching PHI** → the covered CRM's own automations, or self-hosted n8n on AWS. Never Zapier/Make.
- **Hosting a marketing site (no PHI)** → anything; no BAA needed.
- **Hosting an app that receives PHI** → Vercel Pro+BAA or AWS, not a no-BAA tier.
- **AI must read PHI** → AWS Bedrock under AWS BAA (see `hipaa.md` Path B).

*Not legal advice. Confirm vendor terms and your architecture with a compliance officer before protected data flows.*
</vendor_baa_matrix>
