# Measured-rate reference tables

Processed tables read by `scripts/external_rate_validation*.py`, `scripts/p7b_abundance_valid.py` and related scripts. Each was derived from a public source by renaming columns or filtering rows; no values were changed.

| File | Content | Source |
|---|---|---|
| `k562_ttseq_synthrate.csv` | K562 TT-seq synthesis rates per gene | GEO GSE229305 (Todorovski et al., NAR Cancer 2024), `GSE229305_K562_TTseq_synthesis_measurements_rates.txt.gz` |
| `todorovski_k562_halflife.csv`, `halflife_thp1.csv` | K562 and THP-1 SLAM-seq half-lives | GEO SuperSeries GSE229314 (Todorovski et al., NAR Cancer 2024, doi:10.1093/narcan/zcae039), Supplementary Table 10, sheets `K562_UT` and `THP1_UT` |
| `halflife_molm13.csv`, `halflife_rnadecaycafe_k562.csv` | MOLM-13 and K562 degradation rates | RNADecayCafe, Zenodo record 15785218, `AvgKdegs_genes_v1.csv`, filtered by `cell_line` |
| `k562_schwalb_ttseq_synthrate_grch38.csv`, `..._inclNA.csv` | K562 TT-seq synthesis rates per gene, second source | Written by `scripts/external_rate_validation_schwalb_grch38.py` from GEO GSE75792 (Schwalb et al., Science 2016) transcription units matched to GENCODE v24 genes; `_inclNA` keeps units without a biotype |
| `housekeeping.txt` | Housekeeping gene symbols | Eisenberg and Levanon, https://www.tau.ac.il/~elieis/HKG/HK_genes.txt (first column) |

## Files to download before re-running the GSE75792 comparison

`scripts/external_rate_validation_schwalb_grch38.py` also reads two annotation files that are not included because of their size:

```bash
cd data
curl -O https://ftp.ncbi.nlm.nih.gov/geo/series/GSE75nnn/GSE75792/suppl/GSE75792_transcript.annotation.gtf.gz
curl -O https://ftp.ebi.ac.uk/pub/databases/gencode/Gencode_human/release_24/gencode.v24.annotation.gtf.gz
# gene-level subset
zcat gencode.v24.annotation.gtf.gz | awk -F'\t' '/^#/ || $3=="gene"' | gzip > gencode.v24.genes.gtf.gz
```
