# Reproduction Guide — Tribal Preference Study V15

Generated: 2026-06-18T22:42:43.454373+00:00

## TL;DR

1. Open the notebook in Google Colab or a comparable GPU-backed environment.
2. Mount Google Drive.
3. Confirm the project root exists:

   `/content/drive/MyDrive/tribal_pref_v11`

4. Place GGUF model files under:

   `/content/drive/MyDrive/tribal_pref_v11/models/large/`
   `/content/drive/MyDrive/tribal_pref_v11/models/small/`
   `/content/drive/MyDrive/tribal_pref_v11/models/quant_ablation/`

5. Run the notebook from the beginning if reproducing everything.
6. If the expensive model-generation and judging artifacts already exist, resume from the later analysis cells. The pipeline is designed to be cache/checkpoint friendly.
7. For the completed human-calibrated version, run Phase 10.3 onward after placing the completed workbook in the human-calibration folder.

## Final study scope

The final headline analysis is **Primary-4**.

Headline candidate families:

llama, qwen, gemma, yi

Headline judge families:

llama, qwen, gemma, yi

Full/sensitivity families available in the broader project:

llama, qwen, falcon, gemma, yi

Excluded from headline:

falcon

Falcon, if present, is not part of the Primary-4 headline result. It should be treated only as a diagnostic or sensitivity case.

## Human calibration scope

The completed human calibration uses:

- 400 shared blinded pairwise comparisons
- 2 independent human annotators
- 800 total human judgments
- A/B/Tie labels
- confidence scores from 1 to 5

Completed workbook expected at:

`/content/drive/MyDrive/tribal_pref_v11/analysis/human_calibration/annotator_sheets_completed_primary4_400.xlsx`

Hidden key expected at:

`/content/drive/MyDrive/tribal_pref_v11/analysis/human_calibration/human_annotation_key_hidden_primary4_400.csv`

The completed workbook must contain three sheets:

- `annotator_1`
- `annotator_2`
- `annotator_3`

Each sheet must contain 400 rows and the columns:

- `row_number`
- `annotation_id`
- `annotator_id`
- `prompt`
- `response_A`
- `response_B`
- `human_choice`
- `confidence_1_to_5`
- `notes_optional`

The current import cell normalizes:

- `A1` or `A` → `A`
- `B1` or `B` → `B`
- `Tie` → `Tie`

## Hardware

Recommended for full reproduction:

- GPU: 80 GB VRAM or higher for comfortable local inference with large GGUF models
- RAM: 64 GB minimum; 120 GB preferred
- Disk: enough space for GGUF model files, cached responses, judgments, and release artifacts
- Google Drive or equivalent persistent storage for checkpoints

For analysis-only reproduction:

- Use GPU for the full model-generation and LLM-judgment reproduction path
- Phases 6–12 are mostly pandas/statistics/file-output steps and can reuse cached outputs without loading models

## Software

Recommended packages:

- Python 3.10+
- pandas
- numpy
- scipy
- scikit-learn
- statsmodels
- sentence-transformers
- datasets
- huggingface_hub
- llama-cpp-python
- openpyxl
- matplotlib
- krippendorff, optional
- irrCAC, optional

Important note:

- GPU/CUDA is needed only for local model inference.
- Human calibration import and final publication artifacts do not require GPU.

## Required GGUF files

The notebook uses `MODEL_REGISTRY` to locate model files. Put matching GGUF files under the relevant folder.

### Large models

Folder:

`/content/drive/MyDrive/tribal_pref_v11/models/large/`

- Meta-Llama-3.1-70B-Instruct  → candidates: Meta-Llama-3.1-70B-Instruct-Q4_K_M.gguf, Meta-Llama-3.1-70B-Instruct-Q3_K_M.gguf  [family: llama]
- Qwen2.5-72B-Instruct  → candidates: Qwen2.5-72B-Instruct-Q4_K_M.gguf, Qwen2.5-72B-Instruct-Q3_K_M.gguf  [family: qwen]
- Gemma-2-27B-it  → candidates: gemma-2-27b-it-Q4_K_M.gguf, Gemma-2-27B-it-Q4_K_M.gguf  [family: gemma]
- Yi-1.5-34B-Chat  → candidates: Yi-1.5-34B-Chat-Q4_K_M.gguf  [family: yi]
- Falcon-40B-Instruct  → candidates: falcon-40b-instruct.i1-Q4_K_M.gguf, falcon-40b-instruct.Q4_K_M.gguf, Falcon-40B-Instruct-Q4_K_M.gguf, falcon-40b-instruct-q4_k_m.gguf  [family: falcon]

### Small models

Folder:

`/content/drive/MyDrive/tribal_pref_v11/models/small/`

- Meta-Llama-3.1-8B-Instruct  → candidates: Meta-Llama-3.1-8B-Instruct-Q4_K_M.gguf  [family: llama]
- Qwen2.5-7B-Instruct  → candidates: Qwen2.5-7B-Instruct-Q4_K_M.gguf  [family: qwen]
- Gemma-2-9B-it  → candidates: gemma-2-9b-it-Q4_K_M.gguf  [family: gemma]
- Yi-1.5-9B-Chat  → candidates: Yi-1.5-9B-Chat-Q4_K_M.gguf  [family: yi]
- Falcon-7B-Instruct  → candidates: falcon-7b-instruct.Q4_K_M.gguf, Falcon-7B-Instruct-Q4_K_M.gguf, falcon-7b-instruct-q4_k_m.gguf  [family: falcon]

### Quantization ablation models

Folder:

`/content/drive/MyDrive/tribal_pref_v11/models/quant_ablation/`

- Meta-Llama-3.1-70B-Instruct (Q3_K_M)  → candidates: Meta-Llama-3.1-70B-Instruct-Q3_K_M.gguf  [family: llama]
- Qwen2.5-72B-Instruct (Q3_K_M)  → candidates: Qwen2.5-72B-Instruct-Q3_K_M.gguf  [family: qwen]

## Key generation settings

Candidate generation:

- max_tokens: 768
- temperature: 0.7
- top_p: 0.95

Judge generation:

- max_tokens: 512
- temperature: 0.0
- top_p: 1.0
- context window: 8192

Random seed:

`20260502`

## Prompt-source targets

Approximate prompt target counts:

- total: 200
- MT-Bench: 80
- AlpacaEval: 70
- WildBench: 50

The exact counts should be verified from the generated prompt-master and audit files.

## Pipeline phases

### Phase 1 — Bootstrap and configuration

Sets up:

- paths
- random seeds
- logging
- model registry
- `FILES`
- `PATHS`
- `run_step`
- atomic write helpers
- cache/checkpoint behavior

### Phase 2 — Data pipeline

Builds prompt sources and the prompt master.

Expected work:

- fetch or load MT-Bench
- fetch or load AlpacaEval
- fetch or load WildBench
- normalize prompts
- create stable prompt IDs
- create confirmatory/exploratory split

### Phase 3 — Model loading and response generation

Generates candidate model responses.

Expected work:

- stage/load GGUF model files
- generate responses for large models
- generate responses for small models
- generate quantization-ablation responses
- run artifact scans and quality checks
- cache generated responses

### Phase 4 — Trial construction

Builds pairwise comparison trials.

Expected work:

- construct A/B candidate pairs
- create stable trial IDs
- create AB/BA ordering
- verify candidate-family coverage
- save trial master

### Phase 5 — LLM judgment collection

Collects judge decisions.

Expected work:

- rubric prompt judgments
- neutral prompt judgments
- small-judge judgments
- logprob caching if available
- judge-output parsing
- judge-output artifact diagnostics

### Phase 6 — Effective winners and preference matrices

Builds clean Primary-4 and Full-5 analysis artifacts.

Expected work:

- reconcile AB/BA judgments
- create effective winners
- create Primary-4 preference matrix
- create Full-5 appendix/sensitivity matrix
- alias headline files to Primary-4 explicitly

### Phase 7 — Core inference

Computes the headline statistics.

Expected work:

- TPS summary
- cluster bootstrap
- prompt-level permutation
- per-family BH correction
- Bradley-Terry residual/quality controls
- technical verification of saved outputs

### Phase 8 — Mechanism decomposition

Runs mechanism and robustness regressions.

Expected work:

- style similarity matrix
- regression feature construction
- nested decomposition models
- quasi-binomial/fractional-logit/GEE-style robustness
- separation diagnostics
- optional mixed model

### Phase 9 — Robustness suite

Runs sensitivity analyses.

Expected work:

- multiverse with candidate-subset axis
- neutral-vs-rubric prompt comparison
- contamination/freshness split
- confirmatory holdout
- judge-scale comparison
- quantization ablation
- Falcon diagnostic/sensitivity analysis
- ranking/leaderboard impact simulation
- final technical audit

### Phase 10 — Human calibration

For a fresh annotation run:

1. Run Cell 10.1 to build the 400-row blind annotation sample.
2. Run Cell 10.2 to export annotator-specific files.
3. Send files to human annotators.

For the completed annotation run:

1. Place the completed workbook at:

   `/content/drive/MyDrive/tribal_pref_v11/analysis/human_calibration/annotator_sheets_completed_primary4_400.xlsx`

2. Do not rerun 10.1 or 10.2 unless regenerating the sample.
3. Run:

   - 10.3 — import completed workbook
   - 10.4 — inter-human agreement
   - 10.5 — human majority consensus
   - 10.6 — human vs LLM panel comparison
   - 10.7 — final human calibration summary

### Phase 11 — Final report bundle

Builds the final machine-readable summary:

- headline design
- Primary-4 scope
- robustness outputs
- human calibration outputs
- interpretation guardrails
- paper framing

### Phase 12 — Publication artifacts

Builds:

- figures
- LaTeX tables
- Hugging Face style dataset artifacts
- `MODEL_CARD.md`
- `DATA_STATEMENT.md`
- `REPRODUCE.md`
- final integrity audit

## Current recommended run order after human annotation is complete

If generation and core statistics already exist, run:

1. Cell 10.3
2. Cell 10.4
3. Cell 10.5
4. Cell 10.6
5. Cell 10.7
6. Cell 11
7. Cell 12.1
8. Cell 12.2
9. Cell 12.4
10. Cell 12.5
11. Cell 12.6

Do not rerun expensive generation or judgment cells unless their outputs are missing or intentionally invalidated.

## Outputs of record

Important release artifacts:

- `/content/drive/MyDrive/tribal_pref_v11/release/MODEL_CARD.md`
- `/content/drive/MyDrive/tribal_pref_v11/release/DATA_STATEMENT.md`
- `/content/drive/MyDrive/tribal_pref_v11/release/REPRODUCE.md`
- `/content/drive/MyDrive/tribal_pref_v11/analysis/reports/final_report_primary4_human_calibrated.json`

Important human-calibration artifacts:

- `/content/drive/MyDrive/tribal_pref_v11/analysis/human_calibration/human_import_validation_primary4_400.json`
- `/content/drive/MyDrive/tribal_pref_v11/analysis/human_calibration/human_agreement_metrics_primary4_400.json`
- `/content/drive/MyDrive/tribal_pref_v11/analysis/human_calibration/human_consensus_summary_primary4_400.json`
- `/content/drive/MyDrive/tribal_pref_v11/analysis/human_calibration/human_vs_llm_panel_primary4_400.json`
- `/content/drive/MyDrive/tribal_pref_v11/analysis/human_calibration/human_calibration_summary_primary4_400.json`

Important tables:

- `/content/drive/MyDrive/tribal_pref_v11/tables/table1_main_tps.tex`
- `/content/drive/MyDrive/tribal_pref_v11/tables/table2_decomposition.tex`
- `/content/drive/MyDrive/tribal_pref_v11/tables/table3_robustness.tex`
- `/content/drive/MyDrive/tribal_pref_v11/tables/table4_human_calibration.tex`

## Common pitfalls

### 1. GPU not available

If no GPU is available, do not run the model-generation or LLM-judgment phases. Cached downstream analysis/publication-artifact cells may still be inspected, but the release notebook is GPU-first.

### 2. llama-cpp-python CUDA wheel not installed

Run the GPU setup cell to install the CUDA-enabled `llama-cpp-python` wheel, then restart runtime before loading models. Do not use a source-build fallback for the paper-facing run.

### 3. Missing completed human workbook

If Cell 10.3 fails, confirm that the workbook is named exactly:

`annotator_sheets_completed_primary4_400.xlsx`

and placed at:

`/content/drive/MyDrive/tribal_pref_v11/analysis/human_calibration/annotator_sheets_completed_primary4_400.xlsx`

### 4. Human labels use A1/B1 instead of A/B

This is expected. Cell 10.3 normalizes A1/B1/Tie to A/B/Tie.

### 5. Old hard-coded Mixtral references

Older publication-artifact cells may contain hard-coded `mixtral` rows. Replace those cells with the dynamic Primary-4 versions so the notebook reads families from `MODEL_REGISTRY`.

### 6. Old bootstrap key names

Older table cells may expect:

- `tps_point`
- `tps_ci_lo`
- `tps_ci_hi`

Current outputs may instead use:

- `observed_tps`
- `ci_95_low`
- `ci_95_high`

Use the updated Cell 12.2, which supports both schemas.

### 7. Final report key missing

If `FILES["final_report"]` is missing, use the updated Cell 11. It defines:

`final_report_primary4_human_calibrated.json`

### 8. Rerunning cached cells

Most cells use `run_step` and write output files. If outputs exist and `force=False`, they can be skipped. If `force=True`, the cell will recompute. For expensive phases, set `force=False` after successful completion if you want to avoid reruns.

## Integrity expectations

A successful final run should confirm:

- Primary-4 headline files exist.
- Falcon is not included in the headline analysis.
- 400 shared human annotation items were imported.
- 800 final human judgments from two retained annotators were validated.
- Human agreement metrics were written.
- Human consensus/no-consensus labels were created.
- Human-vs-LLM comparison was written.
- LaTeX tables include the human calibration table.
- MODEL_CARD, DATA_STATEMENT, and REPRODUCE files were written.
- Final audit passes or clearly lists only non-fatal warnings.

## Repository and release

Code:

- this notebook

Data release:

- Hugging Face dataset folder or equivalent release folder, depending on final venue requirements

Preprint:

- arXiv recommended after the manuscript is coherent and all final audit files pass

Pre-registration:

- OSF or repository timestamp can be added if desired, but do not claim formal pre-registration unless it was actually timestamped before analysis.
