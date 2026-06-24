<hipaa_framework>
## Purpose
The HIPAA-specific framework: when HIPAA triggers, which Anthropic/Claude surfaces are eligible, the compliant AI-on-PHI paths, and the recommended architecture patterns. Built on top of `compliance-principles.md`. Pair with `vendor-matrix.md` for the per-vendor lookup.

Verified against Anthropic's Trust Center / privacy docs, AWS and Google Cloud HIPAA pages, and HHS guidance (2025–2026). Re-verify before relying on it; vendor policies change.

---

## What HIPAA protects

**PHI (Protected Health Information):** individually identifiable health information — name + health condition, treatment, intake forms, therapy notes, claims/insurance data, anything tying a person to care.

**Business associate:** any entity that creates, receives, maintains, or transmits PHI on behalf of a covered entity. Business associates MUST be under a signed **BAA (Business Associate Agreement)**.

**The trigger:** if a system (including an AI) touches PHI, that vendor is a business associate and needs a BAA. If it never touches PHI, no BAA is needed for that work. (See `compliance-principles.md` Principles 2 & 3.)

---

## Claude / Anthropic surfaces — HIPAA eligibility

| Surface | BAA eligible? | Condition |
|---|---|---|
| Claude.ai web, **Free / Pro / Max / Team** | **NO** | Consumer plans are explicitly excluded from the BAA |
| Console / Workbench (web UI) | **NO** | Excluded |
| First-party Claude **API** (Messages API) | **YES** | Signed BAA (via sales); requires 30-day retention; Covered Models only |
| Claude **Enterprise** (sales-assisted / AWS Marketplace) | **YES** | Admin accepts BAA in settings |
| Self-serve Enterprise | **NO** | Can't enable HIPAA through that flow |
| **Claude Code** via **Pro/Max subscription login** | **NO** | Consumer plan excluded; bundled Claude Code seats not covered |
| **Claude Code** via **API key on a BAA account** | **YES** | Requires **Zero Data Retention (ZDR) enabled**; CLI only (not desktop-remote / web beta) |
| Claude via **AWS Bedrock** | **YES** | Under your **AWS BAA** (signed self-service in AWS Artifact) |
| Claude via **Google Vertex AI** | **YES** | Under your **Google Cloud BAA** |

**The nuance that traps people:** a Claude Code session authenticated by a Pro/Max subscription is NOT covered, no matter what. To put Claude Code under a BAA you must either (a) use an API key on a commercial BAA account with ZDR enabled, or (b) route inference to AWS Bedrock / Google Vertex under that cloud's BAA.

**Asymmetry worth knowing:** the base Anthropic API requires **30-day retention** (not ZDR) to be HIPAA-covered, but **Claude Code** requires **ZDR enabled** to be covered. Different clauses. Match the exact config each surface needs.

---

## The three compliant AI-on-PHI paths (only when the AI MUST touch PHI)

Default is to keep the AI out of the PHI path entirely (Principle 6). When the AI genuinely must process PHI:

**Path A — Anthropic API + BAA + ZDR.** Get a commercial API key, sign the BAA (contact Anthropic sales), enable Zero Data Retention. Point Claude Code at the API key instead of the subscription.

**Path B — AWS Bedrock under AWS BAA (most common).** Sign the AWS BAA self-service in AWS Artifact. Run Claude on Bedrock (a HIPAA-eligible service). Point Claude Code at it with `CLAUDE_CODE_USE_BEDROCK=1` + AWS region/credentials. Inference happens inside your AWS boundary; Anthropic is a sub-processor with zero operator access. **Note:** when Bedrock mode is on, the subscription is NOT the inference path — AWS credentials are. Compliance comes from Bedrock + the AWS BAA, not from the Claude plan.

**Path C — Google Vertex AI under Google Cloud BAA.** Same idea on GCP: `CLAUDE_CODE_USE_VERTEX=1`, covered under Google Cloud's BAA.

**Caveat:** the cloud BAA covers the model inference call, not the entire Claude Code product. Some server-side tools (web search, code execution, Files API, MCP connector) are unsupported on Bedrock/Vertex and may route to first-party infra — outside the cloud BAA. For PHI workflows, stick to the supported Messages-API surface.

---

## Recommended architecture: the two-lane pattern (the default answer)

For nearly every healthcare builder, the right move is to keep the AI OUT of the PHI path:

**Lane 1 — No PHI (built + managed by the AI):** marketing website, content, lead-gen (non-PHI), SOPs, internal docs, non-clinical business lines. No BAA needed.

**Lane 2 — PHI (lives only in a BAA-covered system, e.g. GoHighLevel HIPAA tier or a compliant EHR):** client intake, records, clinical workflows, automations. The AI never enters this lane.

**The intake-form pattern (critical):** host the marketing site on a normal host, but make the **intake form a native form from the BAA-covered platform** (e.g. an embedded GoHighLevel form) so PHI flows **directly into the covered environment** and never touches the marketing host's server. The website is the picture frame; the covered platform is the safe.

Result: one BAA, one place PHI lives, AI never touches PHI. Cheapest and most defensible.

---

## Common healthcare-stack facts (verify before relying on)

- **GoHighLevel:** HIPAA add-on (~$297/mo) includes a BAA. Not compliant by default; must be enabled per sub-account.
- **TherapyNotes (EHR):** HIPAA-compliant, signs a BAA, but has **no official public API** — automating into it relies on unofficial bridges, a real compliance + reliability risk. Flag this; it limits any automation tool, not just AI.
- **Zapier / Make:** do NOT sign BAAs — keep PHI out of them. Use the covered platform's own automations, or self-hosted n8n on AWS under the AWS BAA.
- **AWS / Bedrock:** HIPAA-eligible, BAA self-service via AWS Artifact.

See `vendor-matrix.md` for the full table.

---

## Anti-patterns specific to HIPAA

- **Putting PHI through a Pro/Max Claude subscription.** Not covered. Ever.
- **"Host the folder in AWS to make it HIPAA."** Hosting files ≠ covering the inference. The compliant AWS route is Bedrock under a BAA (Path B), not just an EC2 box.
- **Routing PHI through Zapier/Make** because they're convenient. No BAA = violation.
- **Letting the AI read an intake export "just to help."** That puts PHI on a non-covered endpoint. Keep it in Lane 1.

*Not legal advice. Confirm with a compliance officer / counsel before any PHI flows.*
</hipaa_framework>
