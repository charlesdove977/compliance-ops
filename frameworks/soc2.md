<soc2_framework>
## Purpose
SOC2 framework. **Status: roadmap stub (coming soon).** The framework-agnostic core in `compliance-principles.md` already applies to SOC2 builds; the SOC2-specific vendor mappings, Trust Services Criteria checklist, and architecture patterns are in active development.

---

## What SOC2 covers (orientation)

SOC2 is an auditing framework for service organizations, built on five **Trust Services Criteria**: Security, Availability, Processing Integrity, Confidentiality, and Privacy. Unlike HIPAA (a law with a defined protected-data class), SOC2 is an attestation that your controls operate effectively. A SOC2 Type II report is the common ask from enterprise customers before they trust you with their data.

## How the core principles apply now

Until the full framework ships, Compliance Ops applies the framework-agnostic core (`compliance-principles.md`) to SOC2 builds:
- Treat customer data as the protected class (Principle 2 — touching, not storing, triggers scope).
- Two-lane separation of customer data from everything else (Principle 4).
- Route customer data only through vendors with the right attestations / DPAs (Principle 5).
- Keep the AI out of the customer-data path unless explicitly covered (Principle 6).
- Document the data-flow + vendor checklist for the auditor (same as HIPAA).

## Coming soon
- SOC2 vendor attestation matrix (which build-stack vendors hold SOC2 Type II + offer DPAs).
- Trust Services Criteria → architecture-control mapping.
- Audit-evidence checklist generator.
- Additional regimes on the roadmap: **GDPR, PCI-DSS, CCPA**.

*Not legal advice. SOC2 readiness must be confirmed with your auditor.*
</soc2_framework>
