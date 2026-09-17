# Who Judges Matters

Reproducibility materials for the preprint **Who Judges Matters: Measuring Family-Conditioned Preference in LLM-as-Judge Panels**.

## Summary

LLM-as-a-judge evaluations are often treated as though the evaluator were neutral. This study tests whether a judge gives more support to responses from its own model family after holding the candidate family fixed. The Primary-4 analysis covers Llama, Qwen, Gemma, and Yi across 194 prompts and 9,312 order-counterbalanced judgments.

The saved analysis gives a global family-preference score of **0.067** (95% CI: 0.053 to 0.084), or 6.7 percentage points. This repository includes the final notebook and the aggregate outputs used to verify that headline value.

## Repository contents

- `notebooks/`: clean, unexecuted copy of the final Colab notebook.
- `data/aggregates/`: the Primary-4 preference matrix and summary. These contain aggregate results only.
- `docs/`: reproduction instructions, data availability information, and a release audit.
- `requirements.txt`: a starting environment for the notebook.

## Reproduction

Full generation and judging require the upstream prompts, the model weights, a GPU-backed environment, and sufficient storage for cached outputs. Follow [docs/REPRODUCE.md](docs/REPRODUCE.md) for the original run design and set `PROJECT_ROOT` to a directory you control before running the notebook.

For a lightweight result check, load `data/aggregates/tps_summary_primary.json`. It contains the 4 by 4 judge-by-candidate matrix and the reported Primary-4 score. The underlying prompt- and response-level records are intentionally not redistributed here.

## Data availability and licensing

The study draws on MT-Bench, AlpacaEval, and WildBench. This repository does not redistribute their raw prompts, model responses, or human annotation material. Users must obtain upstream resources directly and comply with their licenses and terms.

The FastChat, AlpacaEval, and WildBench codebases are released under Apache 2.0. WildBench's dataset card identifies the benchmark data as CC BY 4.0. See [docs/DATA_AVAILABILITY.md](docs/DATA_AVAILABILITY.md) and [NOTICE](NOTICE) for details.

## Citation

If you use these materials, please cite the associated preprint:

```bibtex
@article{awuni2026whojudgesmatters,
  title={Who Judges Matters: Measuring Family-Conditioned Preference in LLM-as-Judge Panels},
  author={Awuni, David Ababio and Achenie, Luke E. K. and Partey, Benjamin Tei and Owusu, Elvis Gyasi and Sowah, Nii-Nai Derrick},
  year={2026},
  note={Preprint submitted to arXiv}
}
```

## License

The code and original aggregate outputs in this repository are available under the Apache License 2.0. Third-party resources remain subject to their own terms.
