<disclaimer>
## Purpose
The non-negotiable framing that must accompany every compliance opinion this skill produces. Surface a short form of this at the end of any compliance answer, and include the full version in any generated document.

## The Three Hard Truths (always state these)

**1. This is not legal advice.**
Compliance Ops encodes well-researched engineering and architecture principles. It is not a lawyer, not a compliance officer, and not a certification. Every real decision about whether a system is compliant must be confirmed by the user's own compliance officer or qualified counsel before protected data flows through it.

**2. This does NOT make a Claude consumer subscription able to process protected data.**
A Claude Pro / Max / Team subscription is explicitly excluded from Anthropic's Business Associate Agreement. No skill, framework, prompt, or configuration changes that. Compliance Ops helps you *build* with compliance principles in mind — it does not change which plan tiers are eligible to handle PHI. If the AI itself must process protected data, that requires a covered endpoint (e.g. AWS Bedrock under an AWS BAA, or the Anthropic API under a BAA with Zero Data Retention) — see `hipaa.md`.

**3. A guardrail is not a guarantee.**
Compliance Ops keeps protected data out of the AI's path and routes it through vendors that have signed the right agreement. It dramatically reduces the chance of a leak. It cannot promise zero risk, and it cannot cover misconfiguration, human error outside the build, or vendor failures. It is a seatbelt and a paper trail, not a force field.

## Short form (paste at the end of any compliance opinion)
> *Compliance Ops is a build-time guardrail, not legal advice, and it cannot make a Claude consumer subscription able to process protected data. Confirm the final architecture with your compliance officer or counsel before any protected data flows.*
</disclaimer>
