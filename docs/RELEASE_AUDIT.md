# Release Audit

The public notebook is a clean code copy: execution outputs and widget metadata were removed before release. A local scan found no embedded Hugging Face, OpenAI, or GitHub credential values.

The aggregate `tps_summary_primary.json` reports 4,656 reconciled observations from 194 prompts. Recomputing the mean of the four within-family minus cross-family contrasts yields 0.0674398625, which rounds to the paper's reported 0.067.

Full model inference has not been rerun as part of this repository release. The notebook retains the documented pipeline; cached raw artifacts remain private pending upstream-license review.
