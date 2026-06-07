Here's the ported module. A few design notes on what changed and why:

**What was ported and rewritten**

- `_validate_headers` / `_suggest_header_corrections` / `_ask_user_correction` → merged into `validate_headers()`, which returns a `{original: corrected}` mapping applied during CSV reading. No `TSVRow` or `BiomarkerEntry` involved — works directly on `dict` rows from `csv.DictReader`.
- `_preflight_validation` → split into `validate_headers()` + `check_biomarker_ids()`, keeping the same two checks (header correction prompts, sequential ID assignment) without any of the pipeline-specific setup (`Metadata`, `ApiCallType`, etc.).
- The condition name / entity name matching logic → rewritten as `check_name_id_consistency()`. The original did an API round-trip to compare TSV names against a canonical resource name. The offline equivalent groups rows by ID and flags any ID that maps to more than one display name across rows — catches the same class of inconsistency without the API dependency.

**What was added** (gaps in the original that made sense to fill for a standalone QC tool)

- `check_required_fields()` — the original silently skipped rows with an empty `assessed_biomarker_entity_id`; here it's an explicit error.
- `check_evidence_sources()` — validates the `DATABASE:ACCESSION` format that `_handle_evidence` assumed but never checked.
- `check_duplicates()` — catches exact duplicate rows.
- `QCReport` — a simple collector that separates errors from warnings and can write a plain-text report file, useful for the volunteer to share back with you.

**What was intentionally left out**

Everything that touches `Metadata`, `ApiCallType`, `BiomarkerEntry`, `BiomarkerComponent`, `TSVRow`, `Evidence`, and the JSON writing logic — none of that is relevant to QC and would drag in the whole `format-converter` dependency tree.
