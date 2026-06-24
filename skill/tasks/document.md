<purpose>
Generate the two artifacts a compliance officer actually wants: a data-flow map (where protected data goes and who touches it) and a vendor agreement checklist (which vendors are in scope and whether each has signed the right agreement).
</purpose>

<user-story>
As a business owner facing a compliance review, I want a clear document showing exactly where protected data flows and which vendors are covered, so that my compliance officer can sign off quickly instead of guessing.
</user-story>

<when-to-use>
- User runs `/compliance-ops document`.
- After an interview or audit, to capture the result for the compliance officer.
- Whenever the user needs audit-ready paperwork.
</when-to-use>

<context>
@frameworks/compliance-principles.md
@frameworks/disclaimer.md
@templates/data-flow-map.md
@templates/vendor-baa-checklist.md
</context>

<references>
@frameworks/vendor-matrix.md
</references>

<steps>

### 1. Gather the flow
Use the classification from the interview/audit (or build it now): every surface, Lane 1 vs Lane 2, and the protected-data path.

### 2. Fill the data-flow map template
Populate `templates/data-flow-map.md`: each surface, whether it touches protected data, which lane, which vendor, and the agreement status. Show the protected-data path explicitly (collected → systems → stored).

### 3. Fill the vendor agreement checklist
Populate `templates/vendor-baa-checklist.md`: every vendor in scope, the agreement required (BAA/DPA), signed yes/no, tier, and the action item if not yet signed.

### 4. Write it out
Save both as a dated document. Default location for Charlie OS / this repo context: a `compliance/` folder in the project, or wherever the user keeps compliance records. Offer to render it as a Google Doc (via the available Google Doc tooling) if the user wants to share it.

### 5. Include the full disclaimer
Embed the full disclaimer from `frameworks/disclaimer.md` in the document — this is paperwork that may be forwarded to counsel.
</steps>

<output>
- A dated data-flow map (surfaces, lanes, vendors, agreement status).
- A vendor agreement checklist with action items.
- Full disclaimer embedded.
</output>

<acceptance-criteria>
- [ ] Both artifacts produced and saved.
- [ ] Protected-data path shown explicitly.
- [ ] Every in-scope vendor listed with agreement status + action item.
- [ ] Full disclaimer embedded in the document.
</acceptance-criteria>
