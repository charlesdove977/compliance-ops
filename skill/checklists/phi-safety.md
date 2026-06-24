<phi_safety_checklist>
## Purpose
The pre-build tripwire checklist. Run this BEFORE the AI builds anything that could touch protected data. Each item is a tripwire — if it fires, STOP, surface it plainly, and do not build through it. This is the "seatbelt" half of Compliance Ops.

The first block is phrased for HIPAA/PHI; the same logic applies to any protected-data class. Regime-specific tripwires for SOC 2, GDPR, and PCI-DSS follow below — run whichever regimes apply (more than one can).

---

## Tripwires — STOP if any fires (HIPAA / general)

- [ ] **AI about to read protected data.** Is Claude being pointed at a file, folder, export, or paste that contains real PHI (intake records, notes, claims)? → STOP. Keep the AI in Lane 1. PHI goes through covered vendors, not the AI, unless on a covered endpoint (Bedrock+BAA).

- [ ] **No-BAA tool in the protected path.** Is a tool that does NOT sign a BAA (Zapier, Make, an uncovered host) being placed where it would receive or transmit PHI? → STOP. Swap for a covered vendor or redesign so it never touches PHI.

- [ ] **PHI landing on an uncovered host.** Will a custom form or app POST PHI to a server/host that has no BAA? → STOP. Use the covered platform's native form, or put the host under a BAA (Principle 2: receiving counts even without storing).

- [ ] **Subscription Claude in the PHI path.** Is the plan a Claude Pro/Max/Team subscription AND is PHI about to enter the AI's context? → STOP. Consumer subscriptions are never BAA-eligible. Covered endpoint required.

- [ ] **"It's local / we don't store it" reasoning.** Is the justification "the files are local" or "it just passes through, we don't save it"? → STOP and re-check. Neither exempts a system (Principles 1 & 2).

- [ ] **Hosting-equals-compliance reasoning.** Is the plan "host it in AWS so it's HIPAA"? → STOP. Hosting files ≠ covering the data/inference path. The covered agreement must cover the surface that actually touches PHI.

---

## SOC 2 tripwires — STOP if any fires

- [ ] **Secrets in the repo / plaintext env.** API keys, DB creds, tokens committed to git or baked into a client bundle? → STOP. Vault them. (Most common CC6 finding.)
- [ ] **Customer data → consumer AI endpoint.** Customer data about to hit consumer Claude.ai/Pro instead of a commercial API/Bedrock under a DPA? → STOP. Use a covered endpoint.
- [ ] **New vendor with no SOC 2 + DPA.** A sub-processor entering the data path without its own SOC 2 Type II + an executed DPA? → STOP. Verify or swap; add to the sub-processor list.
- [ ] **No audit logging on customer-data access/changes.** → STOP. Add logging now; Type II needs evidence across a period.

## GDPR tripwires — STOP if any fires

- [ ] **No lawful basis / no DPA.** EU personal data being collected with no documented Art. 6 basis, or piped to a processor with no signed DPA? → STOP.
- [ ] **US transfer with no SCCs/DPF.** EU personal data sent to a US endpoint (AI, analytics, email) with no transfer mechanism? → STOP. Route AI through a DPA'd commercial tier with SCCs/DPF.
- [ ] **No deletion/export path.** System can't actually fulfill erasure or portability (deletion doesn't cascade to processors/backups)? → STOP, build it.
- [ ] **Special-category data on a weak basis.** Health/biometric/etc. without explicit consent? → STOP.

## PCI-DSS tripwires — STOP if any fires

- [ ] **Raw card number (PAN) touching your own server/API.** A custom card form POSTing PAN to your backend? → STOP. Use the processor's hosted fields / redirect (SAQ A).
- [ ] **AI anywhere near card data.** The AI about to read, log, or echo a PAN or CVV? → STOP. The AI stays entirely outside the CDE; it builds around the tokenized flow.
- [ ] **Logging or storing PAN / SAD.** PAN in logs/DB, or CVV/track/PIN stored at all? → STOP. Store token + last4 + brand only; SAD is never stored.
- [ ] **Rolling your own card form** instead of the processor's hosted field? → STOP. Defeats scope reduction; exposes you to skimming liability.

---

## Green-light (safe to build) when

- [ ] Every surface that touches protected data carries the right coverage for its regime (BAA / SOC 2+DPA / DPA+transfer / PCI processor), OR is redesigned out of the protected path.
- [ ] The AI is confirmed in Lane 1 (no protected data), OR on a covered endpoint with the reason documented. For PCI, the AI is entirely outside the CDE.
- [ ] No uncovered tool sits anywhere in the protected-data path.
- [ ] The protected-data path is mapped and ready for the data-flow document.

If all green-lights pass and no tripwire fired, build. Otherwise, resolve first.

*Not legal advice. A passed checklist is a build-time safeguard, not a compliance certification.*
</phi_safety_checklist>
