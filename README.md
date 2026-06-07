# biomarker-qc

Standalone QC module for biomarker TSV files.

## Usage

```bash
python biomarker-qc.py <file.tsv>
python biomarker-qc.py <file.tsv> --report qc_report.txt
```

## Checks

| # | Check | Level |
|---|-------|-------|
| 1 | **Required-field completeness** — flags rows where any required field is missing or blank | ERROR |
| 2 | **Name/ID consistency** — detects cases where the same ID is paired with different display names across rows (covers `condition_id`, `assessed_biomarker_entity_id`, `exposure_agent_id`, `specimen_id`) | WARNING |
| 3 | **Evidence-source format** — verifies every `evidence_source` value follows `DATABASE:ACCESSION` format (e.g. `PubMed:12345678`) | ERROR |
| 4 | **Duplicate row detection** — flags exact duplicate rows | WARNING |

### Required fields

`biomarker_id`, `biomarker`, `assessed_biomarker_entity`, `assessed_biomarker_entity_id`, `assessed_entity_type`, `best_biomarker_role`, `evidence_source`, `evidence`

## Output

A summary is always printed to stdout. Pass `--report <path>` to also write it to a file.

```
============================================================
QC SUMMARY  —  2 errors, 1 warnings, 0 info
============================================================
[ERROR] (row 4) [evidence_source]: evidence_source 'PubMed' does not match expected 'DATABASE:ACCESSION' format.
[ERROR] (row 9) [biomarker_id]: Required field 'biomarker_id' is empty.
[WARNING] (file) [condition]: ID 'DOID:1612' (condition_id) is paired with multiple names ...
============================================================
```

## Exit codes

| Code | Meaning |
|------|---------|
| `0` | No errors (warnings may be present) |
| `1` | One or more ERRORs found |
