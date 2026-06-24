# Data-Flow Map — {project-name}

**Prepared by:** Compliance Ops (build-time guardrail, not legal advice)
**Date:** {date}
**Regime:** {HIPAA | SOC2 | other}
**Protected data class:** {PHI | customer data | …}

---

## Surfaces

| Surface | Touches protected data? | Lane | Vendor | Agreement status |
|---|---|---|---|---|
| {e.g. Marketing website} | No | 1 | {Vercel} | None needed |
| {e.g. Client intake form} | Yes | 2 | {GoHighLevel (native form)} | BAA — {signed / pending} |
| {e.g. Onboarding automation} | Yes | 2 | {GoHighLevel workflows} | BAA — {signed / pending} |
| {e.g. AI build assistant} | No | 1 | {Claude Code} | Out of protected path |

---

## Protected-data path (explicit)

```
{Patient fills intake form}
   → {GoHighLevel HIPAA environment (BAA)}      [enters covered system here]
   → {stored in GoHighLevel / pushed to EHR}    [lives here]

AI (Claude Code): NEVER in this path — Lane 1 only.
```

---

## Notes / risks
- {e.g. EHR has no official API — automation into it uses an unofficial bridge; flagged for review.}
- {e.g. If AI must process PHI later, route via AWS Bedrock under AWS BAA.}

---

## Disclaimer
{Embed full disclaimer from frameworks/disclaimer.md — not legal advice; does not make a Claude consumer subscription able to process protected data; confirm with compliance officer / counsel before protected data flows.}
