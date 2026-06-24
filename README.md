<p align="center">
  <img src="assets/banner.png" alt="Compliance Ops — compliance guardrail for Claude Code (HIPAA active; SOC 2, GDPR, PCI-DSS coming soon)" width="100%">
</p>

# Compliance Ops

> Compliance guardrail for [Claude Code](https://claude.ai/code). Makes your AI build with HIPAA (and soon SOC 2) principles baked in — protected data stays out of non-compliant servers and flows, by design. A seatbelt and a paper trail, **not legal advice**.

<p>
  <a href="https://www.charlieautomates.com/charlie-os-vs/"><img src="https://img.shields.io/badge/Work_with_Charlie-Charlie_OS-7c3aed?style=for-the-badge&logo=anthropic&logoColor=white" alt="Work with Charlie"></a>
  <a href="https://www.npmjs.com/package/compliance-ops"><img src="https://img.shields.io/npm/v/compliance-ops?color=blue&label=npm" alt="npm version"></a>
  <a href="https://www.npmjs.com/package/compliance-ops"><img src="https://img.shields.io/npm/dt/compliance-ops?color=blue&label=downloads" alt="npm downloads"></a>
  <a href="LICENSE"><img src="https://img.shields.io/npm/l/compliance-ops?color=green" alt="MIT license"></a>
  <a href="https://github.com/charlesdove977/compliance-ops/stargazers"><img src="https://img.shields.io/github/stars/charlesdove977/compliance-ops?style=flat" alt="stars"></a>
</p>

**A compliance guardrail framework for Claude Code.** It makes your AI aware of compliance rules *before* it builds anything — so every website, workflow, automation, and system it creates for you is designed with those principles baked in. Protected data never gets routed through non-compliant servers or flows, and you rest assured nothing leaks through a tool that was never allowed to touch it.

It is a **seatbelt and a paper trail** — not a compliance certificate.

> ⚠️ **Read this first.** Compliance Ops is **not legal advice**, and it does **not** make a Claude consumer subscription (Pro / Max / Team) able to process protected health information without breaking HIPAA. Those plans are excluded from Anthropic's Business Associate Agreement, and no skill changes that. What Compliance Ops does is help you **build with the right compliance principles and frameworks in mind**, and keep protected data out of the AI's path. Your compliance officer or counsel makes the final call.

---

## What it does

Most people bolt compliance on *after* they've built something — and by then the protected data has already flowed through three tools that never should have touched it. Compliance Ops flips that. It sits in front of the build:

1. **It interviews you first.** When you invoke it, it asks two questions: **what are you building?** and **what are you worried about?** (HIPAA / patient data, SOC2, finance, "not sure"). 
2. **It loads the matching framework.** HIPAA today; SOC2 and more on the way. The framework teaches your AI exactly when the regulation triggers and which vendors are actually covered.
3. **It designs the build correct-by-default.** Protected data is separated into its own "lane" and routed only through vendors that have signed the right agreement (a BAA). The AI is kept *out* of the protected-data path entirely, unless you deliberately put it on a covered endpoint.
4. **It trips a wire if something's wrong.** If the AI is about to read patient records, or wire a no-BAA tool (like Zapier) into a flow carrying protected data, or POST that data to an uncovered host — it stops and tells you, before the build happens.
5. **It documents the boundary.** It generates a data-flow map and a vendor agreement checklist you can hand straight to your compliance officer.

The result: the AI builds and manages everything *around* your protected data — the website, the forms, the automations — without the protected data ever passing through it or through a vendor that isn't covered.

---

## How to activate it

Compliance Ops responds to **plain words, skills, or commands** — however you naturally work:

**Just talk to it.** It auto-activates on natural language:
- *"Is this HIPAA compliant?"*
- *"Can I put patient data through this?"*
- *"Build this intake form HIPAA safe."*
- *"I'm building a behavioral health intake system — what do I need to worry about?"*
- mentions of **compliance, HIPAA, PHI, BAA**

**Or call the command directly:**

| Command | What it does |
|---|---|
| `/compliance-ops` | Interview + guard a new build |
| `/compliance-ops audit` | Audit an existing system for compliance gaps |
| `/compliance-ops document` | Generate the data-flow map + vendor BAA checklist for your compliance officer |

Once it's active, anything the AI builds in that session is shaped by the loaded framework — automatically.

---

## What's inside

```
compliance-ops/
├── SKILL.md                          # Entry point — activation, persona, routing
├── tasks/
│   ├── interview.md                  # Default: ask what + worry, then guard the build
│   ├── audit.md                      # Trace an existing flow, rank the gaps
│   └── document.md                   # Generate compliance-officer paperwork
├── frameworks/
│   ├── compliance-principles.md      # Regime-agnostic core (the decision flow)
│   ├── hipaa.md                      # HIPAA: triggers, eligible Claude surfaces, patterns
│   ├── vendor-baa-matrix.md          # Which vendors sign a BAA, on which tier
│   ├── soc2.md                       # SOC2 (roadmap stub — coming soon)
│   └── disclaimer.md                 # The non-negotiable framing
├── templates/
│   ├── data-flow-map.md              # Where protected data goes + who touches it
│   └── vendor-baa-checklist.md       # Vendor-by-vendor agreement status
└── checklists/
    └── phi-safety.md                 # Pre-build tripwires — STOP if any fires
```

---

## The core idea (in one diagram)

```
   LANE 1 — NO PROTECTED DATA            LANE 2 — PROTECTED DATA
   (the AI builds + manages this)        (lives only in covered vendors)

   • Marketing website                   • Intake form (native covered form)
   • Content, code, SOPs                 • Records, notes, claims
   • Lead-gen, non-regulated lines       • Regulated automations

   No agreement needed.                  Covered by a signed BAA.
   AI works here freely.                 AI never enters unless on a covered endpoint.
```

Compliance Ops' whole job is to keep that wall standing and to prove it's standing on paper.

---

## Roadmap

- ✅ **HIPAA** — live
- 🔜 **SOC2** — in development (Trust Services Criteria mapping + vendor attestation matrix)
- 🔜 **GDPR, PCI-DSS, CCPA** — on the roadmap

More compliance frameworks are being added over time. The framework-agnostic core already applies to all of them; the regime-specific vendor mappings and checklists are rolling out.

---

## Install

**Via npm (recommended):**

```bash
npx compliance-ops install            # installs to ~/.claude/skills/compliance-ops/
npx compliance-ops install --project  # installs into ./.claude/ for the current project
npx compliance-ops install --with-commands   # also writes /compliance-ops slash-command stubs
```

Other commands: `npx compliance-ops update` (overwrite), `uninstall`, `where`, `--help`.

**Manual:** copy the `skill/` directory's contents into `~/.claude/skills/compliance-ops/` (so `SKILL.md` sits at the root of that folder).

**Charlie OS:** ships inside [Charlie OS](https://charlieautomates.com) by default — no separate install needed.

---

## Related projects

- **[Charlie OS](https://www.charlieautomates.com/charlie-os/)** — one-click Claude Code setup that bundles BASE, CARL, PAUL, SEED, Skillsmith, and 32 curated skills (Compliance Ops included). If you want Compliance Ops *plus* the rest of Charles's stack on day one, install Charlie OS instead.
- **[Work with Charlie](https://www.charlieautomates.com/charlie-os-vs/)** — done-for-you install, custom skill builds, and 1:1 Claude Code engineering for founders and agency operators (including regulated-industry builds).

---

## The honest disclaimer (again, because it matters)

Compliance Ops is a **build-time engineering guardrail**. It is **not legal advice**, it is **not a compliance certification**, and it **cannot** make a Claude consumer subscription handle protected health information without breaking HIPAA. It reduces the chance of a leak by keeping protected data out of the AI's reach and routing it only through covered vendors — but the final compliance determination always belongs to your compliance officer or qualified counsel.

---

Built by [Charles Dove](https://charlieautomates.com) · Charlie Automates.
