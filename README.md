# Artifacts: Single-Agent vs. Multi-Agent LLM Grading for Vietnamese E-commerce Essays

Artifact repository of the FISAT 2026 paper *"Single-Agent vs. Multi-Agent LLM Grading for Vietnamese E-commerce
Essays: A Cross-Model Study with Aggregation-Strategy Ablation"* (T. T. Trinh, T. D. Nguyen, N. U. Nguyen).

All **520** submissions (A1 137, A2 129, A3 123, A4 131) were scored by two human TAs; GT = mean of the two scores.
Systems: single-agent and multi-agent (Content / Structure / Language specialists + Chairman) on **GPT-5.2** and
**DeepSeek-V4-Flash**. Student identifiers are anonymized (`S0001`-`S0520`, random, no link to real IDs).

## Reproduce (no private data needed)
```bash
pip install -r requirements.txt
python scripts/build_release.py --repo .                 # QWK exactly as in the paper (observed-label mode)
python scripts/build_release.py --repo . --qwk-labels fixed   # sensitivity: full 0-10 label set
```
Output: `results/*.csv`, `results/fig3_pred_vs_gt.png`, `results/fig4_cross_model_qwk.png`, `results/verification_report.csv`.

## Paper -> file map
| Paper item | File |
|---|---|
| Table 1 (dataset, human agreement) | `results/table1_dataset_human.csv` |
| Table 2 (single-agent GPT-5.2) | `results/table2_single_agent_gpt52.csv` |
| Table 3 (multi-agent GPT-5.2) | `results/table3_multi_agent_gpt52.csv` |
| Table 4 (bias, Pearson, Spearman) | `results/table4_bias_and_rank_correlation.csv` |
| Table 5 (specialist disagreement) | `results/table5_specialist_disagreement.csv` |
| Table 6 (aggregation ablation) | reported in the paper; `results/table6_aggregation_ablation.csv` is produced when per-submission specialist outputs are supplied |
| Table 7 / Fig. 4 (cross-model) | `results/table7_cross_model.csv`, `results/fig4_cross_model_qwk.png` |
| Fig. 3 (pred vs GT) | `results/fig3_pred_vs_gt.png` |
| Section 5 numbers vs recomputed | `results/verification_report.csv` |

## Data (`data/`)
| File | Content |
|---|---|
| `scores_master.csv` | one row per submission: `assignment, student_id, rater1, rater2, gt, single_gpt52, multi_gpt52, single_deepseek, multi_deepseek` (+ `*_raw`, `*_source` provenance columns) |
| `specialists_deepseek.csv` | DeepSeek multi-agent specialist totals, spread, veto (A3 only) |
| `specialists_gpt52.csv` | GPT-5.2 per-submission specialist totals (only if provided at build time) |
| `specialists_gpt52_summary.csv` | GPT-5.2 specialist aggregates per assignment (veto rate, mean spread, specialist means) used for Table 5 when per-submission totals are not released |
| `per_criterion_deepseek.csv` | per-criterion scores `tc1..tc5` of the DeepSeek runs (no free text) |
| `data_quality_log.csv` | every reconciliation done while building the master table |

Scores: GPT-5.2 totals are multiples of 0.5. DeepSeek raw totals (multiples of 0.25) are rounded **half-up to the nearest 0.5**
(`single_deepseek`, `multi_deepseek`); the unrounded value is kept in `*_raw`.

## Metrics
QWK on integer-rounded scores (GT rounded to nearest integer, `np.rint`), computed with the label set that occurs in the data
(equivalent to scikit-learn's default, used in the paper). MAE, bias, Pearson and Spearman use exact values. 95% CIs: 1000
submission-level bootstrap resamples (percentile, seed 42); CIs may differ from the paper in the second decimal.
`results/qwk_label_sensitivity.csv` also gives QWK on the full 0-10 label set.

## Data policy
No raw submissions, names or student numbers are released. IDs are random; the mapping is not public. Free-text feedback
columns were excluded. See `DATA_POLICY.md`.

## Numerical reproduction
Reproduced values agree with the paper to three decimals, except for A3 (including the A3 DeepSeek QWK), where differences are at most 0.01. The A3 statistics in the paper were computed on 124 records, which include one placeholder record that is not a graded submission; this artifact uses the 123 valid submissions. Item-level comparison: `results/verification_report.csv`.

## Data availability
* Raw submissions, free-text feedback and real student identifiers are not released (see `DATA_POLICY.md`).
* GPT-5.2 specialist outputs are provided as per-assignment aggregates (`data/specialists_gpt52_summary.csv`, Table 5); per-submission values are not part of this release.
* DeepSeek specialist outputs are provided for A3 (paper limitation L4).
* `agents/` contains the pipeline definition and per-stage skill descriptions; re-running the LLM grading additionally requires API access and the original submissions.

## Cite
See `CITATION.cff`. Please cite the paper (FISAT 2026) when using these data or code.

```bibtex
@inproceedings{trinh2026grading,
  title     = {Single-Agent vs. Multi-Agent LLM Grading for Vietnamese E-commerce Essays: A Cross-Model Study with Aggregation-Strategy Ablation},
  author    = {Trinh, Tin T. and Nguyen, Truc D. and Nguyen, Nhi U.},
  booktitle = {Proceedings of FISAT 2026},
  year      = {2026}
}
```
(Update volume, pages and DOI when the proceedings are published.)

## License
Code: MIT (`LICENSE`). Data in `data/` and `results/`: CC BY 4.0.
