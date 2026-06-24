# Vendor Agreement Checklist — {project-name}

**Prepared by:** Compliance Ops (build-time guardrail, not legal advice)
**Date:** {date}
**Regime:** {HIPAA | SOC2 | other}

Every vendor that touches protected data must appear here with the required agreement signed.

| Vendor | Role in system | Agreement required | Signed? | Tier / condition | Action item |
|---|---|---|---|---|---|
| {GoHighLevel} | {Intake + PHI storage + automations} | BAA | {Yes/No} | {HIPAA add-on enabled} | {Enable add-on + sign BAA} |
| {Vercel} | {Marketing host} | None (Lane 1, no PHI) | n/a | {Pro} | {Keep PHI off host} |
| {AWS} | {Bedrock, only if AI-on-PHI} | BAA | {Yes/No} | {Artifact BAA} | {Sign in AWS Artifact} |
| {Zapier / Make} | {—} | N/A — does NOT sign BAA | — | — | {Keep PHI out / swap} |

---

## Summary
- **In-scope vendors (touch protected data):** {list}
- **Covered + signed:** {list}
- **Outstanding action items:** {list}

---

## Disclaimer
{Embed full disclaimer from frameworks/disclaimer.md.}
