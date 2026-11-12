# velocity-reliability-map

Analysis code and result tables for

> Ka-Kyung Kim, Jaemyun Lyu, Geongyu Lee, Sejin Park, Yong Gi Ji. **A Reliability Map for Per-Gene Multiome RNA Velocity Parameters in Single-Cell Kinetics.** Machine Learning for Health (ML4H) 2026, Findings Track. [OpenReview](https://openreview.net/forum?id=eGy4QuhHdP)

Chromatin-informed ("multiome") RNA velocity models report per-gene transcription rates α, degradation rates γ and a chromatin-to-transcription lag. The paper audits these outputs for reproducibility across methods, refits and datasets, and for agreement with measured rates, and summarizes the result as a reliability map. Every number in the paper is produced by a script in this repository from the per-gene result tables in `results/`.

The release `ml4h2026-camera-ready` is the version cited in the paper.

## Layout

| Folder | Contents |
|---|---|
| `scripts/` | Human HSPC pipeline. `p1_*` preprocessing, `p2_*` velocity fits (RNA-only scVelo floor, MultiVelo, MultiVeloVAE, MoFlow, CRAK-Velo) and refits, `p3_*` concordance, profile likelihood and dissociation, `p4_*` permutation FDR, `p5_*` to `p8_*` follow-up analyses, `p10*` cell × gene velocity-matrix audit, `p17_*` and `p18_*` analyses added during review, `external_rate_validation*.py` comparisons with measured rates |
| `cross_dataset/` | Builders, fits and concordance scoring for the five external multiomes, including the preregistered mouse gastrulation scorecard (`p3_prereg_gse205117.py`) |
| `preregistration/` | The sealed preregistration documents (mouse gastrulation; velocity-matrix audit), as written before the corresponding fits existed |
| `results/` | Per-gene fitted parameters per method and dataset (`*_genes*.csv`), the 12 MultiVelo refit tables (`bootstrap_refit/`), and the machine-generated output of every analysis (`*.gen.md`, `*.csv`, `*.json`) |
| `figures/` | Scripts for the figures in the paper |
| `data/` | Processed measured-rate reference tables used by the external comparisons (see `data/README.md`) |
| `env/` | Conda environment specifications and lock files |

## Where the main numbers come from

| Claim | Script | Output |
|---|---|---|
| Cross-method α +0.882 and lag, Δρ (Figure 1) | `scripts/p3_identifiability_dissociation.py`, `scripts/p17_post_review_robustness.py` | `results/identifiability_dissociation.csv`, `results/post_review_robustness.gen.md` |
| MultiVelo lag as the priming interval versus the coupled-on length; harmonized lag conventions; abundance-residualized concordance; module bootstrap; curvature coordinates | `scripts/p17_post_review_robustness.py` | `results/post_review_robustness.gen.md`, `results/post_review_robustness.csv` |
| Within-lineage ATAC shuffle and MultiVelo refit stability under the priming definition (lag +0.725; coupled-on +0.624, α +0.949, α_c +0.729) | `scripts/p18_priming_recompute.py` (reads `results/bootstrap_refit/refit_*.csv`) | `results/priming_recompute.gen.md`, `results/priming_recompute.csv` |
| Profile-likelihood curvature; 236 of 538 genes (44%) boundary-pinned or degenerate | `scripts/p3_profile_likelihood.py` | `results/profile_likelihood_identifiability.csv`, `results/profile_likelihood_freed.csv` |
| External replication on five datasets | `scripts/p3_crossdataset_concordance.py`, `scripts/p3_moflow_external_concordance.py`, `cross_dataset/p3_concordance_*.py` | `results/concordance_*.gen.md`, `results/concordance_*.csv` |
| Preregistered six-item scorecard (mouse gastrulation) | `cross_dataset/p3_prereg_gse205117.py` | `results/prereg_gse205117_scorecard.csv` |
| α and abundance against K562 TT-seq synthesis, two sources (GSE229305; GSE75792 on GRCh38) | `scripts/p7b_abundance_valid.py`, `scripts/external_rate_validation_schwalb_grch38.py` | `results/external_rate_validation_schwalb_grch38.md` and `_*.csv` |
| γ against SLAM-seq half-lives | `scripts/external_rate_validation.py` | `results/external_rate_validation_gamma.csv` |
| Velocity-matrix audit | `scripts/p10*.py` | `results/velocity_matrix_*.json` |

`scripts/external_rate_validation_schwalb.py` and its outputs `results/external_rate_validation_schwalb.*` are kept for the record only. That version matched the GSE75792 transcription units, which are in GRCh38 coordinates, to GENCODE v19 (hg19) genes, and is superseded by the GRCh38 script above.

## Quick check

The table-level analyses run on CPU from the files in `results/` and `data/` in seconds to minutes; no raw data are needed.

```bash
conda env create -f env/scv-preprocess.yml
conda run -n scv-preprocess python scripts/p17_post_review_robustness.py --selftest
conda run -n scv-preprocess python scripts/p18_priming_recompute.py
```

`p18_priming_recompute.py` stops with an assertion error if the coupled-on values it recomputes differ from the reported ones (shuffle ρ 0.721, refit lag +0.624, refit α +0.949).

Re-running the velocity fits requires the raw data below and a GPU for MultiVeloVAE, MoFlow and CRAK-Velo. Shell drivers use the placeholders `/path/to/repo`, `/path/to/data` and `/path/to/home`; set these first. Comments in the code are partly in Korean.

## Data

All data are public: human HSPC 10x Multiome GSE209878 (primary); human fetal cortex GSE162170; human BMMC GSE194122; macrophage differentiation GSE284047; mouse gastrulation GSE205117; the 10x Genomics "Fresh Embryonic E18 Mouse Brain 5k" demo; measured-rate references GSE229305, GSE229314 and GSE75792. `scripts/download_data.sh` fetches the primary dataset. The fitted AnnData objects are not included because of their size; the per-gene parameters they contain are in `results/`.

## Citation

See `CITATION.cff`. Please cite the paper and the archived release.

## License

MIT. See `LICENSE`.
