> English translation for reviewers, added 2026-10-13 during the review period. The Korean original `PREREGISTRATION_gse205117.md` in this folder is the sealed version; if the two differ, the original governs.

# Preregistration — 5th cross-dataset replication test on GSE205117 (mouse gastrulation)

> **Sealed: 2026-07-12.** Before any velocity fit or cross-method concordance has been produced for GSE205117 (GEX fastq currently being processed, ATAC not yet processed, no alpha/lag exists). These predictions are genuinely held-out. **The commit hash seals the timestamp.**
> Nature: the **5th preregistered confirmatory replication** of the "alpha robust / lag fragile" ordering already observed in 4 systems (HSPC, human_brain, E18, BMMC, macrophage). Not discovery (honest labeling, advisor). The strengthening point = **is lag still fragile even at maximal priming (gastrulation)?**
> ⚠️ Discarded strong prediction: "profile-likelihood curvature predicts reproducibility beyond SNR" — judged indefensible in the SNR test (`results/identifiability_vs_snr.md`), so it is not preregistered.

## Test design
- Data: GSE205117 full B (E7.5/E8.0/E8.5/E8.75, rep1 each, GEX+ATAC 10x Multiome). WT developmental trajectory.
- Methods: identical to the existing benchmark — RNA-only floor (scVelo dynamical) + chromatin-aware (MultiVelo, MultiVeloVAE, MoFlow). ATAC is used as the chromatin input.
- Outputs: within-dataset cross-method Spearman rho (alpha and lag separately) + cross-dataset (HSPC<->gastrulation) alpha and lag reproduction. Follows the existing `p3_concordance.py` / `p3_crossdataset_concordance.py` pattern.

## Sealed predictions (fixed before seeing results, falsifiable)
| # | Prediction | Pre-specified threshold | Basis (existing 4 systems) |
|---|---|---|---|
| 1 | **within-dataset cross-method alpha reproduction** | Spearman rho **≥ 0.50** | HSPC 0.88, other systems also high |
| 2 | **within-dataset cross-method lag reproduction** | Spearman rho **≤ 0.15** (≈0) | existing −0.04 to +0.08 |
| 3 | **alpha > lag ordering** | alpha rho − lag rho **≥ 0.35** | holds in all systems |
| 4 | **cross-dataset HSPC<->gastrulation** | cross alpha **> +0.2** AND cross alpha > cross lag | E18 +0.32/+0.10, BMMC +0.55/+0.05 |
| 5 | **per-gene reproduction gap** | lag discordance median > alpha discordance median | HSPC 0.317 vs 0.078 |
| 6 | **fragile even at maximal priming** | #2 and #3 hold in gastrulation (strong lineage priming) | strengthens the headline |

> **Decision log — MoFlow arm inclusion confirmed (2026-07-13, author, before any fit was produced).**
> The per-gene lag discordance of prediction 5 is scored with the **original HSPC definition (MultiVelo vs MoFlow)** — not substituted by MV vs MultiVeloVAE (alpha stays MV vs VAE, as in the original definition). `p2_moflow.py` produces `results/moflow_genes_gse205117.csv`; when the scorer (`cross_dataset/p3_prereg_gse205117.py`) detects this file, it automatically scores with the original definition (the code branch already exists). This decision was recorded at a time when not a single GEX/ATAC fit existed, so held-out integrity is preserved.

## Falsification criteria (if wrong, report as wrong — no post-hoc rescue)
- **lag cross-method rho ≥ 0.50** → "fragile even in the priming best case" **fails**. If lag is reproduced, our core claim breaks in this system → report honestly and restate with narrowed conditions (no post-hoc lowering of the threshold, e.g. to alpha < 0.5).
- **alpha cross-method rho < 0.30** → alpha robustness fails (weakens the overall thesis) → report honestly.
- **Reversal of the alpha > lag ordering** (lag rho > alpha rho) → ordering hypothesis fails.
- In these cases, **a failure is treated as a failure**, as with the skin (#5) DEFER.

## Clarification of interpretation level (predictions unchanged)
- This test asks "is lag reproduced by current velocity methods", not "is lag biology absent". Deeper sequencing, temporal resolution, and metabolic labeling may later make lag identifiable; our claim is a boundary on **what can be trusted with current methods**.
- The mechanism (§8) honestly states that lag, being the **difference of two switch-times, is low-SNR and sloppy** (no overclaim that identifiability transcends SNR; see `identifiability_vs_snr.md`).

## Status
- GSE205117 download and GEX processing in progress (overnight). ATAC processing, multi-method fits, and concordance will follow (days). **When results are produced, record pass/fail for each prediction in this table as is.**
