# Methodology Notes

## Why grouped splits matter here

Early versions of the classifiers in this pipeline used a plain stratified
train/test split (`module2_classifier.py`). AMP sequence databases contain
many near-duplicate analog families (e.g. single-residue variants of the
same designed peptide series). A plain split lets near-duplicates land on
both sides of the split, inflating reported AUC. `module2_classifier_v3.py`
onward use CD-HIT-derived cluster IDs with `GroupShuffleSplit`, so an entire
analog family stays on one side of train/val/test. The reported AUC dropped
from an ungrouped 0.9611 to a grouped ~0.93–0.94 — that drop is the honest
number, not a regression to explain away.

The same fix was independently applied to the hemolysis classifier
(`toxicity_classifier_v2.pkl`) after the same near-duplicate pattern was
found across HemoPI-1's main/validation split.

## Why the RF oracle needed a hard-negative retrain (v4 → v5)

`amp_classifier_v4.pkl` was trained against fully random negative sequences.
On closer inspection its predictions correlated strongly with charge and
isoelectric point within the negative class alone (r≈0.44 charge, r≈0.39 pI)
— consistent with the classifier substantially having learned "is this
sequence cationic" rather than "is this sequence an AMP." `amp_classifier_v5`
was retrained against Swiss-Prot-derived negative fragments matched to the
positive class's length distribution, and reports a lower but more honest
test AUC (0.9079 vs. 0.9374). This is a documented, ongoing limitation, not
a solved problem — see the main README.

## What "NSGA-II" means in this pipeline, precisely

`module10_multiobjective_v3.py` imports pymoo's `NSGA2`, `SBX`, `PM`, and
`FloatRandomSampling`, but `minimize()` is never called with them in the
scripts that produced the current results (`pareto_candidates_v3.csv`).
What actually runs is **non-dominated sorting** (`NonDominatedSorting`) over
an already-generated candidate pool (VAE + Transformer output) — i.e. this
step ranks candidates that already exist, it does not evolve new sequences
via genetic search. An earlier version (`module10_multiobjective.py`)
does run a genetic search, but over a continuous 7-dimensional
physicochemical property space, not directly over sequence space, and its
output was not the source of the current Pareto front. Stating this
precisely because "Multi-Objective Optimisation" can otherwise imply the
GA search happened on the reported candidates, and it didn't.

## Toolchain split: why MD isn't in the conda environment

All pre-MD pipeline steps (data curation, classifiers, generative models,
Pareto ranking) run in Anaconda on Windows, with GPU-heavy generative model
training offloaded to Google Colab (Drive-backed checkpoints). MD simulation
(MARTINI 3 coarse-graining + GROMACS) runs separately in WSL2/Ubuntu, because
GROMACS in this project's version is not conda/pip-installable in a way
that's reliable across platforms. Long GROMACS production runs are run
inside `tmux` sessions, decoupled from any interactive session, with state
serialized via `pickle` so they survive disconnects.

## Debugging principle used throughout MD analysis

Two real bugs were caught during MD trajectory analysis using the same
diagnostic: **an effect that is identical in magnitude across every
candidate regardless of peptide identity is a systematic artifact, not
biology.** This caught (1) a unit-labeling bug in `analyze_md.py` (MDAnalysis
returns positions in Å, not nm — an earlier version silently mislabeled the
axis), and (2) a leaflet-mixing bug in `lipid_perturbation.py` (comparing
local vs. bulk lipid density across both membrane leaflets systematically
biased the ratio below 1.0 regardless of any real local perturbation). Real,
peptide-specific biology should differ between candidates; identical
artifacts should not.
