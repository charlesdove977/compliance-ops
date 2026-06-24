<phi_safety_checklist>
## Purpose
The pre-build tripwire checklist. Run this BEFORE the AI builds anything that could touch protected data. Each item is a tripwire — if it fires, STOP, surface it plainly, and do not build through it. This is the "seatbelt" half of Compliance Ops.

Phrased for HIPAA/PHI; the same logic applies to any protected-data class (swap "PHI" for the relevant class).

---

## Tripwires — STOP if any fires

- [ ] **AI about to read protected data.** Is Claude being pointed at a file, folder, export, or paste that contains real PHI (intake records, notes, claims)? → STOP. Keep the AI in Lane 1. PHI goes through covered vendors, not the AI, unless on a covered endpoint (Bedrock+BAA).

- [ ] **No-BAA tool in the protected path.** Is a tool that does NOT sign a BAA (Zapier, Make, an uncovered host) being placed where it would receive or transmit PHI? → STOP. Swap for a covered vendor or redesign so it never touches PHI.

- [ ] **PHI landing on an uncovered host.** Will a custom form or app POST PHI to a server/host that has no BAA? → STOP. Use the covered platform's native form, or put the host under a BAA (Principle 2: receiving counts even without storing).

- [ ] **Subscription Claude in the PHI path.** Is the plan a Claude Pro/Max/Team subscription AND is PHI about to enter the AI's context? → STOP. Consumer subscriptions are never BAA-eligible. Covered endpoint required.

- [ ] **"It's local / we don't store it" reasoning.** Is the justification "the files are local" or "it just passes through, we don't save it"? → STOP and re-check. Neither exempts a system (Principles 1 & 2).

- [ ] **Hosting-equals-compliance reasoning.** Is the plan "host it in AWS so it's HIPAA"? → STOP. Hosting files ≠ covering the data/inference path. The covered agreement must cover the surface that actually touches PHI.

---

## Green-light (safe to build) when

- [ ] Every surface that touches PHI is a covered vendor on the right tier, OR redesigned out of the PHI path.
- [ ] The AI is confirmed in Lane 1 (no PHI), OR on a covered endpoint with the reason documented.
- [ ] No no-BAA tool sits anywhere in the PHI path.
- [ ] The PHI path is mapped and ready for the data-flow document.

If all green-lights pass and no tripwire fired, build. Otherwise, resolve first.

*Not legal advice. A passed checklist is a build-time safeguard, not a compliance certification.*
</phi_safety_checklist>
