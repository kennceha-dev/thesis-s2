# Implementation Notes: Field-Aware Confidence Routing IDP

This note is for future system building. It explains the intended system in plain engineering language.

## Core Idea

Do not build "OCR then LLM for everything". Build a field-level decision system.

The system should answer this question for every target field:

> Is the current OCR/rule candidate safe enough to accept, or should this field be escalated?

The unit of routing is a field, not a document.

Example: in one receipt, `date` may be accepted from OCR, `tax` may be accepted from rules, but `total_amount` may go to VLM because OCR produced `22,00O`.

## Main Pipeline

```text
input image/pdf
-> preprocess
-> OCR text + bbox + confidence
-> group OCR tokens into rows/regions/tables
-> generate field candidates
-> score confidence per field
-> route per field:
     accept
     text-only LLM
     VLM crop
     needs_review
-> structured JSON
-> compare with ground truth
```

## Important Boundary

OCR does not know fields. OCR only gives text and positions.

Field extraction starts after OCR. We must map OCR tokens into target fields using schema, labels, positions, formats, and context.

## Target Schema

Use a JSON config. This is not cheating. Administrative extraction is usually schema-driven.

Example:

```json
{
  "total_amount": {
    "type": "currency",
    "aliases": ["total", "grand total", "jumlah", "jumlah pembayaran", "total bayar"],
    "risk": "high"
  },
  "tax": {
    "type": "currency",
    "aliases": ["ppn", "pajak", "tax", "vat"],
    "risk": "medium"
  },
  "transaction_date": {
    "type": "date",
    "aliases": ["tanggal", "tgl", "date"],
    "risk": "medium"
  }
}
```

If using CORD or another dataset, map dataset labels to our schema. Do not try to support every label at once. Start with 5-8 fields.

## Candidate Generation

Candidate generation should be simple and auditable.

Use these signals:

1. Label aliases: find labels like `Total`, `PPN`, `Tanggal`.
2. Spatial proximity: if value is right of or below a label, pair them.
3. Regex/type pattern: amounts look like currency, dates look like dates, IDs look like IDs.
4. Table/row position: item rows and summary rows behave differently.
5. Semantic similarity: optional helper for unseen labels like `Jumlah Pembayaran`.

The output of candidate generation is not final truth. It is just candidate evidence.

Candidate object should look like:

```json
{
  "field": "total_amount",
  "raw_value": "22,00O",
  "normalized_value": null,
  "label_text": "Total",
  "value_bbox": [250, 300, 330, 320],
  "label_bbox": [50, 300, 110, 320],
  "ocr_confidence": 0.72,
  "signals": {
    "label_match": 0.95,
    "spatial": 0.90,
    "schema": 0.40,
    "context": 0.30
  }
}
```

## Row and Table Logic

Group OCR tokens into rows by y-coordinate.

Receipt example:

```text
Item              Price
Nasi Goreng       15,000
Es Teh             5,000
Subtotal          20,000
PPN                2,000
Total             22,000
```

Classify rows:

- Rows with summary aliases (`subtotal`, `ppn`, `total`) become summary fields.
- Rows with text description + amount, no summary alias, and above summary area become line items.
- Rightmost numeric token is usually the price/amount.
- Left tokens are usually item name or label.

Expected output:

```json
{
  "line_items": [
    {"name": "Nasi Goreng", "price": 15000},
    {"name": "Es Teh", "price": 5000}
  ],
  "subtotal": 20000,
  "tax": 2000,
  "total_amount": 22000
}
```

## Confidence Formula

Use weighted heuristic first. Do not overbuild ML calibration at prototype stage.

Available components:

- `ocr_quality`: average OCR confidence for candidate tokens.
- `label_match`: exact/fuzzy/semantic match between nearby label and field aliases.
- `spatial_confidence`: label-value distance and direction.
- `schema_validation`: date/currency/id format and normalizability.
- `context_consistency`: subtotal + tax = total if all are available.
- `candidate_agreement`: do multiple extraction routes agree?

If a component is unavailable, ignore it and renormalize weights. Do not set it to zero by default.

Pseudo formula:

```text
score = weighted_average(available_signals)
final_confidence = max(0, score - penalty)
```

Penalties:

- suspicious amount format: medium
- ambiguous chars O/0, I/1, S/5: medium
- subtotal + tax != total: high
- required field missing: high
- low OCR confidence: medium/high

## Routing Logic

The system should route based on both confidence and uncertainty type.

```text
if confidence >= accept_threshold and critical_validation_passed:
    accept
elif text_uncertainty_dominates:
    send OCR context to text-only LLM
elif visual_uncertainty_dominates:
    send expanded crop to VLM
else:
    needs_review
```

Start thresholds:

```text
accept_threshold = 0.85
review_threshold = 0.60
```

Tune thresholds on validation data.

## Text-Only LLM vs VLM

Text-only LLM is for semantic/context problems:

- label is unfamiliar
- multiple candidates exist
- value needs normalization
- OCR is readable but system is unsure which value is correct

VLM is for visual/OCR problems:

- OCR confidence low
- candidate contains suspicious characters
- amount/date format looks broken
- OCR may have dropped a character
- stamp/signature/noise may interfere

Text-only LLM cannot truly recover missing visual evidence. If OCR says `Rp. 15,00` but the image says `Rp. 15,000`, text-only LLM can only guess. VLM or human review is needed.

## VLM Crop Strategy

Never crop only the exact value bbox. It may remove useful context.

Use expanded crop:

- value bbox
- nearest label bbox
- same row
- one row above and below if needed
- margin around the region

Prompt VLM to read only that crop and return JSON for one field.

## LLM Prompt Strategy

Do not make hundreds of if-else prompts. Use templates by field type.

Text LLM prompt variables:

- target field
- expected type
- OCR context
- known accepted fields
- output JSON schema

Example:

```text
Target field: total_amount
Expected type: currency
Known fields:
subtotal = 20000
tax = 2000
OCR context:
Subtotal 20,000
PPN 2,000
Total 22,00O
Return JSON only: {"value": ..., "normalized_value": ..., "confidence": ...}
```

When LLM returns a result, update only that field. Do not replace the whole document output.

## HITL

Human-in-the-loop is not the main contribution. Main contribution is field-aware confidence routing.

Implement HITL as `needs_review` flag first.

Optional later:

- small review UI
- correction memory
- template fingerprint

Ablation can compare:

- without HITL: unresolved fields remain wrong/null
- with HITL simulation: unresolved fields replaced by ground truth and counted as human workload

## Ground Truth and Evaluation

Ground truth should be normalized JSON.

Prediction:

```json
{"total_amount": "Rp. 22.000"}
```

Ground truth:

```json
{"total_amount": 22000}
```

Normalize before comparing.

Metrics:

- field exact match
- F1
- CER/WER for text fields
- numeric error for amount fields
- false accept rate
- escalation rate
- human review rate
- latency
- STP rate
- cost proxy based on number of LLM/VLM calls

Most important metric: false accept rate on high-risk fields. A wrong accepted total is worse than a field sent to review.

## Baselines

Run the same dataset through:

1. OCR-only
2. OCR + rule
3. OCR + LLM all fields
4. Direct VLM all fields
5. Document-level routing
6. Proposed field-level routing

Ablations:

- no schema validation
- no context consistency
- no VLM crop
- no field-level routing
- with/without HITL flag

## First Implementation Plan

Build in this order:

1. Data loader: image + ground truth JSON.
2. OCR wrapper: output tokens with text, bbox, confidence.
3. Row grouper: group by y-coordinate.
4. Field config JSON.
5. Candidate generator for receipt-like data.
6. Normalizers: currency, date, text.
7. Confidence scorer.
8. Router with accept/text_llm/vlm_crop/needs_review.
9. Stub LLM/VLM first, then connect real models.
10. Evaluator.
11. Baseline runner.
12. Error analysis report.

## Implementation Warning

Do not let LLM become the whole system. If LLM extracts everything from the full document, the thesis becomes generic OCR+LLM. The novelty is deciding which field needs stronger processing and why.

The audit trail is important. Every field should store:

```json
{
  "value": ...,
  "normalized_value": ...,
  "candidate_bbox": ...,
  "route": ...,
  "confidence": ...,
  "signals": ...,
  "decision": ...
}
```

This makes the system explainable and evaluable.
