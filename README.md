# AMP-Generation-Using-GEN-AI
A computational pipeline for generation of anti-microbial peptides using generative ai.
# Generative AI Pipeline for Antimicrobial Peptide (AMP) Candidate Design

Computational pipeline for generating and screening antimicrobial peptide (AMP)
candidates: data curation → RF oracle classifiers → VAE + Transformer generative
models → NSGA-II multi-objective Pareto optimization → ESMFold structural
validation → MARTINI 3 coarse-grained MD (GROMACS) membrane-interaction pilot.

Built during an internship at Accubits Invent (Bio360 Life Sciences Park,
Thiruvananthapuram), under Dr. Aswathy U and Dr. Nidhin S.

## What this project is — and isn't

This is a demonstration of methodological rigor in a computational AMP design
pipeline: correct grouped data splits, honest reporting of classifier
performance, documented dead ends, and a structurally-validated Pareto front
of candidate sequences.

**It is not a drug discovery claim.** No candidate here has been synthesized
or assayed. "Activity" and "safety" scores throughout are classifier
predictions, not experimental results. Anywhere this repo says a peptide is
"active" or "safe," read that as "predicted to be" by a specific model with
a specific, stated test AUC — not as a wet-lab fact.

## Pipeline

1. **Data curation** — APD6, dbAMP3, CAMPR4 (natural), Swiss-Prot negatives,
   merged and deduplicated; CD-HIT clustering used for grouped train/val/test
   splits (near-duplicate peptide families do not leak across splits — this
   was not true in an earlier version of this pipeline; see `docs/methodology.md`).
2. **RF oracle classifiers** — AMP activity (`amp_classifier_v5.pkl`, test
   AUC 0.9079 on Swiss-Prot hard negatives) and hemolytic risk
   (`toxicity_classifier_v2.pkl`, HemoPI-1, grouped split).
3. **Generative models** — VAE (posterior collapse fixed via free-bits KL
   floor + beta warmup) and Transformer (memorization-filtered: 8.7% of raw
   Transformer output was exact training-set copies and was removed before
   scoring).
4. **NSGA-II multi-objective optimization** — activity vs. helix propensity
   vs. safety; not a genetic search over sequence space, but non-dominated
   sorting over a generated candidate pool (see `docs/methodology.md` for
   why that distinction matters).
5. **ESMFold structural validation** — pLDDT, secondary structure via
   `pydssp`, amphipathic moment (μH) analysis.
6. **MARTINI 3 coarse-grained MD (GROMACS)** — 100 ns production runs,
   POPE:POPG 75:25 membrane, all 8 filtered candidates.

## Key results (computational predictions only)

All 8 candidates that passed the charge (+2 to +8) / hydrophobicity (30–55%)
biological-plausibility filter completed 100 ns CG-MD. Across all 8: sustained
membrane surface adsorption within 10–20 ns; **no insertion observed at
100 ns**. Two candidates (RRCILRRLC, SRPAIDVRVKMT) showed a transient local
lipid-packing perturbation signal at 80–95 ns, verified across sustained
consecutive frames rather than single-frame artifacts.

`EWKSKLLNSVAKTVL` is the pipeline's preferred lead — it's the only sequence
independently reinforced by three separate stages (multi-objective
optimization, low-complexity filtering, structural/amphipathic analysis) and
has the best predicted safety score of the top candidates. This is a
methodological convergence result, not a synthesis recommendation.

## Known limitations (kept here, not buried)

- The RF activity oracle substantially functions as a charge/pI detector
  (within-negative-class r≈0.44 charge, r≈0.39 pI). The Swiss-Prot
  hard-negative retrain (v5) was the response to this, not a full fix.
- Training script for `amp_classifier_v5.pkl` is not present in this repo —
  a genuine traceability gap, not an omission by choice.
- Two competing ESMFold candidate-selector scripts exist
  (`select_esmfold_candidates.py` vs `filter_for_esmfold.py`) with no clean
  record of which produced the 8 candidates that went into MD. Both are
  included for transparency; `filter_for_esmfold.py` is the one referenced
  in the current results.
- The lipid-perturbation analysis (`lipid_perturbation.py`) is a simple
  circular-shell density proxy, not a rigorous Voronoi-tessellation area-
  per-lipid calculation. Treat it as a first-pass signal, not a final number.

## Repository layout

```
src/
  data_curation/       dataset merging, CD-HIT cluster assignment, augmentation
  classifiers/         RF activity + toxicity classifiers (all versions, dated)
  generative_models/   VAE, Transformer training + generation
  optimization/        NSGA-II Pareto front (v3 is current)
  structure_prediction/ ESMFold submission, candidate filtering, helix-wheel analysis
  md_simulation/       GROMACS/MARTINI setup, trajectory analysis
notebooks/             end-to-end MD pilot notebook
results/               current Pareto front, scored candidates, figures
models/                trained classifier/generative weights (Git LFS)
archive/phase1_rl_esm2/  superseded RL+ESM-2 branch — see its own README
docs/                  methodology notes, full limitations list
DATA_SOURCES.md        licensing/citation for each external database used
```

## Setup

```bash
conda env create -f environment.yml
conda activate amp-pipeline
```

MD simulation steps (`src/md_simulation/`) require GROMACS 2025.4 and
`martinize2`/`vermouth`/`insane` separately — these were run in WSL2, not
the conda environment above. See `docs/methodology.md` for the exact
toolchain split.

## Citation / data sources

See `DATA_SOURCES.md`. If you use this pipeline's code, cite this repo. If
you use the underlying peptide data, cite the original databases (APD6,
dbAMP3, CAMPR4, HemoPI-1) per their own terms, not this repo.

## License

Code: MIT (see `LICENSE`). This license covers the code in this repository
only — it does not extend to third-party peptide data redistributed here,
which remains under its original source licenses (see `DATA_SOURCES.md`).
