# PROGRESS

## 2026-09-29 — Ported seal toggle, proforma invoice and weighing unit from micbac-invoice

**Done** (one commit each)
- Proforma invoice: Invoice Type select on step 1. Proforma prints "PROFORMA INVOICE" title/footer, no packing list, logged to the Sheet as `docType: proforma`; button label and email subject/body follow the type.
- "Include seal & signature" checkbox (beside the document-type bar) for invoice, quotation and letterhead. Off → blank box the same size as the signature image (648×220) so it can be stamped/signed by hand.
- Quotation weighing unit dropdown (MT / KGS / LBS) on the Goods step. Applies to weights only: goods-table Gross Wt header, Quotation Details gross/net labels, and those weights in the file. Qty is always MT and rate always RATE/MT. Invoice weights stay KGS. Legacy drafts with free-text units are mapped onto the dropdown.
- Top button renamed "Commercial Invoice" → "Invoice" (the type is chosen on step 1).

**Decisions / deviations**
- Scope was the three features only. The micbac dead-code cleanup and bug fixes (escaping, port restore, numbering series, etc.) were NOT ported.
- `buildDoc` (jsPDF) is still uncalled dead code here; jsPDF itself is still needed by the payment receipt, so the scripts stay.
- Proformas share the invoice numbering sequence (as before this change).
- The seal toggle does not affect the Payment Receipt (jsPDF, has no signature image).

**Open / next**
- Audit CK for the bugs found in micbac (unescaped user/Sheet data in documents and Records table, port corrupted on draft reload, letter placeholder printed, amount-in-words ≥ 2,000,000, duplicate Sheet record per download, qty float noise, etc.).
