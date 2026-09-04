# Archived: Phase 1 — RL-fine-tuned Transformer + ESM-2 branch

**This branch is not part of the current pipeline and its outputs should not
be cited as this project's results.** It's kept here, clearly separated, for
transparency about what was tried and abandoned — not deleted and not
presented as current work.

## What's here

- `module9_rl.py` — REINFORCE (policy gradient) fine-tuning of the Module 4
  Transformer against a composite reward (AMP score + charge score −
  repeat penalty).
- An ESM-2 fine-tuning branch (`module8`-equivalent work, referenced but not
  reconstructed here).
- `FINAL_CANDIDATES.csv`, `e250_*.csv` — outputs from this branch, dated
  June 19, 2026.

## Why it's archived, not deleted

This branch used a different oracle lineage (an older classifier, v3/v4
generation, and a rule-based hemolysis heuristic later found to be
anti-correlated with the trained toxicity classifier, r≈−0.40 on reference
peptides). It has **zero sequence overlap** with the Pareto front reported
in `results/pareto_candidates_v3.csv`. It was apparently abandoned around
June 19, 2026 without a written decision record — the most likely
reconstruction is that the multi-objective NSGA-II approach in the current
pipeline (`src/optimization/module10_multiobjective_v3.py`) superseded it,
but there's no explicit note confirming that, and this file exists so that
gap in the record is stated rather than papered over.

## If you're reading this to compare approaches

That's a reasonable thing to do — RL-based sequence generation and Pareto-
front ranking of VAE/Transformer output are genuinely different strategies,
and comparing them could be an interesting writeup. Just don't merge their
candidate lists; they came from incompatible oracle versions and aren't
directly comparable on the scores as reported.
