<pci_dss_framework>
## Purpose
The PCI-DSS-specific framework: what it protects, the all-important scope-reduction principle, how to vet payment processors, where the AI fits (entirely out of the card data environment), and the architecture patterns. Built on top of `compliance-principles.md`. Pair with `vendor-matrix.md`.

Verified against the PCI Security Standards Council and processor docs (2025–2026). Current standard: **PCI DSS v4.0.1**. Re-verify before relying on it.

---

## What PCI-DSS protects

**Cardholder Data (CHD)** — at minimum the full **PAN (Primary Account Number)**; may include cardholder name, expiry, service code. CHD may be stored only if protected (encrypted/truncated/hashed/masked, Req. 3).

**Sensitive Authentication Data (SAD)** — full track data, card verification codes (**CVV2/CVC2/CID**), PINs. **SAD must NEVER be stored after authorization.** The hardest line in the standard.

**Who must comply:** anyone who **stores, processes, or transmits** cardholder data, plus anyone who can impact the security of the CDE (cardholder data environment).

**Current version:** **v4.0.1** (published 2024-06-11; v4.0 retired 2024-12-31; v3.2.1 retired 2024). All 64 new/updated requirements became mandatory **2025-03-31**.

**Merchant levels:** L1 >6M tx/yr (QSA RoC + quarterly ASV scan), L2 1M–6M, L3 20K–1M e-comm, L4 <20K e-comm (SAQ, acquirer-set).

**SAQ types (e-commerce):** **SAQ A** (card handling fully outsourced, ~27 controls), **SAQ A-EP** (your page influences the payment but doesn't store CHD, ~191 controls), **SAQ D** (you store/process/transmit CHD — the full standard).

---

## The #1 builder principle — SCOPE REDUCTION: never touch card data yourself

**Never let cardholder data hit your own servers.** Use a PCI-validated processor's **hosted fields / hosted payment page / tokenization** so the PAN goes straight from the customer's browser to the processor, and your backend only ever handles a **token**. This is the payments equivalent of the two-lane "native covered form" pattern: the sensitive field is rendered and owned by the compliant third party, not your code.

**Why SAQ A is the goal:**
- **SAQ A (target)** — card acceptance fully outsourced (full redirect or processor-hosted page). Your servers **never see the PAN**. ~27 controls.
- **SAQ A-EP** — your own page/JS can affect the payment flow (in-page SDK on your domain), even though CHD is tokenized. Script-integrity (Req. 6.4.3) + skimming detection (11.6.1) become critical. ~191 controls — much heavier.
- **SAQ D** — you store/process/transmit CHD directly (PAN on your servers, or a custom card form posting to your API). Effectively the entire standard. Rare, expensive — avoid unless you are a payments company.

> Key nuance: if **your domain serves the page containing the card form** — even an iframe — you are **A-EP at minimum**, because your page's scripts could read keystrokes before data reaches the "secure" field. A **full redirect or processor-hosted page** keeps you in **SAQ A**.

---

## The vendor angle — PCI-validated processors

All below are **PCI DSS Level 1 Service Providers** (audited annually by a QSA). Using their hosted/tokenized components keeps PAN out of your environment. See `vendor-matrix.md`.

| Processor | Hosted / tokenized component | Effect |
|---|---|---|
| **Stripe** | Stripe Checkout (hosted redirect → SAQ A); Elements / Payment Element (in-page hosted fields, tokenized → usually A-EP) | Card field originates from Stripe's PCI-validated servers |
| **Braintree (PayPal)** | Hosted Fields, Drop-in UI → tokenize (SAQ A in most configs) | CHD never passes your servers; backend stores a token |
| **Adyen** | Drop-in, Components, hosted checkout → tokenization | L1 Service Provider; secure input components render the field |
| **Square** | Web Payments SDK (`card.tokenize()` → token to your server) | "Unencrypted payment data never touches your application" |

**Infra hosts (AWS, Vercel, GCP):** being "PCI-eligible" is about infrastructure — it does NOT make putting PAN on them safe. PAN on your own infra = SAQ D / full assessment. The guardrail's answer: **don't put PAN on your infra at all**, regardless of host certification.

---

## Where the AI fits

**The AI must NEVER process, read, log, or store a raw PAN.** Stricter than PHI: the AI is kept **entirely outside the CDE**.

- The AI **builds the checkout UI that embeds the processor's hosted fields / redirect** — it writes the integration, not the card-handling code.
- The AI **never sees a card number.** It works with **tokens, payment-intent IDs, customer IDs, last4, brand, expiry-display** — never PAN or SAD.
- Tokens are safe to flow through AI-built code and your backend. **PAN and SAD are not** — they live only inside the processor's iframe/SDK and the processor's servers.

> Note: "AI never touches the CDE" is **our guardrail principle**, a conservative application of PCI's scope-reduction logic — PCI DSS predates LLMs and does not mention AI. State it as a principle, not a cited clause.

---

## Architecture patterns + decision flow

**Reference pattern (stay SAQ A):**
1. Card field is the **processor's hosted page or iframe** — PAN goes browser → processor, never your server.
2. Processor returns a **token / payment-intent**; your backend charges with the token.
3. Your DB stores **token + last4 + brand + expiry-display only** — never PAN, never SAD.
4. **TLS everywhere** (Req. 4).
5. **Segment the CDE** — isolate anything that ever touches CHD.
6. The **AI builds the surrounding flow** (cart, order, success page, webhooks, token storage) around the tokenized boundary.

```
Need to store/process raw card numbers yourself?
  → Almost never. If somehow yes → SAQ D + QSA. Stop; get a human + QSA.
Use a PCI L1 processor (Stripe / Braintree / Adyen / Square)?  → YES
  └─ Card field fully hosted by processor (redirect or hosted page)?
       YES → SAQ A ✅ target. Backend handles only tokens.
       NO, you render their in-page SDK on your domain → SAQ A-EP (heavier: 6.4.3 + 11.6.1).
             Prefer the hosted/redirect option if SAQ A is achievable.
Never: build your own card form posting PAN to your API.
```

---

## Anti-patterns

1. **POSTing a raw card number to your own API/backend** — instantly drags you into SAQ D.
2. **Logging PAN or SAD** (app logs, analytics, error traces) — and NEVER store CVV/track/PIN under any circumstance.
3. **Storing card numbers in your DB** instead of a processor token (store token + last4 + brand only).
4. **Letting the AI handle, read, or echo card data** — the AI stays out of the CDE; it builds around the tokenized flow.
5. **Rolling your own card input form** instead of the processor's hosted fields — defeats scope reduction, exposes you to skimming liability.
6. **Serving the card form from your own domain** when a redirect would do — silently pushes SAQ A → A-EP.

---

## Honest framing
A build-time guardrail helps you **build payment flows that keep cardholder data out of scope — and out of the AI** (hosted fields, tokenization, SAQ A architecture). It does **NOT** make you PCI-DSS compliant and is **not an assessment** (RoC/SAQ/AoC). Compliance is confirmed by a **QSA and/or your acquirer/processor**, validated annually per merchant level. Not legal or assessment advice. (See `disclaimer.md`.)

Sources: [PCI DSS v4.0.1 announcement](https://blog.pcisecuritystandards.org/just-published-pci-dss-v4-0-1) · [PCI SSC standards](https://www.pcisecuritystandards.org/standards/) · [Stripe PCI guide](https://stripe.com/guides/pci-compliance) · [SAQ A vs A-EP](https://www.schellman.com/blog/pci-compliance/saq-a-vs-saq-a-ep)
</pci_dss_framework>
