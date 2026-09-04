# Data Sources

This project curates peptide sequence data from external databases. The
**code** in this repository is MIT-licensed; the **data** is not — it
belongs to its original sources under their own terms. Before making the
`data/raw/` contents of this repo public, verify current terms for each
source below. Do not assume a database being freely browsable means its
bulk contents are freely redistributable.

| Source | Used for | License / terms (verify before redistribution) |
|---|---|---|
| APD6 (Antimicrobial Peptide Database) | Natural AMP sequences (positive class) | Not independently verified in this repo — check APD's current terms of use before redistributing raw sequence dumps. |
| dbAMP3 | Additional natural AMPs | Not independently verified in this repo — check dbAMP's current terms of use before redistributing raw sequence dumps. |
| CAMPR4 (natural) | Additional natural AMPs, experimentally validated subset | Published under **CC BY-NC** (Creative Commons Attribution-NonCommercial) per the CAMPR4 paper (Gawde et al., NAR 2023). Attribution required; commercial use not permitted under this license. |
| Swiss-Prot | Hard-negative fragments for `amp_classifier_v5` | UniProt/Swiss-Prot data is available under CC BY 4.0 — check current UniProt terms for bulk redistribution specifics. |
| HemoPI-1 (main + validation) | Hemolysis classifier training | Check HemoPI/CAMP authors' current terms before redistributing raw sequence files. |

**Recommendation:** if you're not fully sure a dataset's raw sequences are
clear to redistribute, keep `data/raw/` out of the public repo (add it to
`.gitignore`) and instead document in this file exactly how to re-fetch each
source, so the pipeline is reproducible without you having redistributed
someone else's database.

## Models trained on this data

Classifiers and generative models in `models/` were trained on the merged,
cleaned dataset described above. Model weights inherit no additional license
restrictions beyond what's noted here, but if a source database's terms
prohibit derivative redistribution, that applies to the trained weights too
— re-check before publishing `models/` alongside `data/raw/`.
