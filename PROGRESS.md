# PROGRESS

## 2026-09-29 — Ported seal toggle, proforma invoice and weighing unit from micbac-invoice

**Done** (one commit each)
- Proforma invoice: Invoice Type select on step 1. Proforma prints "PROFORMA INVOICE" title/footer, no packing list, logged to the Sheet as `docType: proforma`; button label and email subject/body follow the type.
- "Include seal & signature" checkbox (beside the document-type bar) for invoice, quotation and letterhead. Off → blank box the same size as the signature image (648×220) so it can be stamped/signed by hand.
- Quotation weighing unit dropdown (MT / KGS / LBS) on the Goods step. Applies to weights only: goods-table Gross Wt header, Quotation Details gross/net labels, and those weights in the file. Qty is always MT and rate always RATE/MT. Invoice weights stay KGS. Legacy drafts with free-text units are mapped onto the dropdown.
- Top button renamed "Commercial Invoice" → "Invoice" (the type is chosen on step 1).

**Decisions / deviations**
- Scope of this first session was the three features only; the micbac bug fixes were ported in the next session (below). The dead-code cleanup was NOT ported.
- `buildDoc` (jsPDF) is still uncalled dead code here; jsPDF itself is still needed by the payment receipt, so the scripts stay.
- Proformas share the invoice numbering sequence (as before this change).
- The seal toggle does not affect the Payment Receipt (jsPDF, has no signature image).

**Open / next** — the micbac bug audit is ported below.

## 2026-09-29 — Ported the micbac bug audit (one commit each)

**Done** (92 browser checks + 12 unit tests pass)
- Amount in words → `js/amount-words.js` (lakh/crore for INR; was "undefined HUNDRED THOUSAND" from 2,000,000). Qty totals → `js/quantities.js` (no float noise, totalled per unit). Both unit-tested: `node --test`.
- Escaped user/Sheet data in the document builders, step-4 summary, goods table, Records table (currency, ids) and named-draft list.
- Ports: drafts, Sheet records **and the address book** all nested "INCCU — …" into the port on each reload; now matched by label (repairs existing data). Empty record port clears the picker instead of keeping the previous one.
- Letter: placeholder no longer printed, empty letter can't be downloaded; letter fields now saved in drafts (body sanitised on restore); date dd.mm.yyyy.
- Records: no duplicate Sheet row per re-download (FNV fingerprint in localStorage `ck_saved_fp`).
- Numbering: separate series `CKLLP/` invoice, `CKLLP/PI/` proforma, `CKLLP/QT/` quotation; new FY restarts at 001; Clone asks the Sheet; numbers upper-cased.
- Quotation: cleared Tolerance stays cleared. Named consignee prints its own name/address (tax code moved under notify party); addresses keep line breaks.
- Progress-bar jumps validate skipped steps. Preview shows the packing list. Email Draft opens one window with an "Open Gmail draft" toolbar link (second popup was blocked).
- `todayISO()` local-time helper (toISOString is UTC).

**Decisions / deviations**
- Invoice numbering with nothing to continue from still defaults to `CKLLP/007/26-27` (previous hard-coded value); other series and other FYs start at 001.
- Already there in CK, so not needed: Agent/Distributor printing, hiding notify address for "to the order", preview "undefined" guard, records-table port escaping.
- Duplicate-record guard is per browser; a different device can still add a row (would need a server-side upsert in Code.gs + redeploy).
- First re-download of each already-saved doc after deploy adds one more row (fingerprint shape changed).
- Not ported: dead-code cleanup (`buildDoc` still unused; jsPDF still needed for the payment receipt).

**Open / next**
- Invoice RATE column always says "/MT", even for rows in another unit (e.g. BAGS).

