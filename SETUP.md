# Setting this up from your local project

This scaffold has the structure, docs, and config files. The actual project
files live on your machine (Anaconda/Windows for most steps, WSL2 for MD) —
run this from there, not from anywhere else.

## 1. Install Git LFS (once per machine)

```bash
git lfs install
```

## 2. Initialize the repo

```bash
cd amp-generative-pipeline        # this scaffold folder
git init
git add .gitattributes            # LFS patterns must be tracked before large files are added
git add .gitignore README.md LICENSE DATA_SOURCES.md environment.yml docs/ archive/
git commit -m "Initial scaffold: docs, license, gitignore, archived Phase 1 branch notes"
```

## 3. Move your actual files in, by category

Copy — don't move originals until you've confirmed the repo looks right —
from your Anaconda project folder into the matching subfolder:

| Move to... | Files |
|---|---|
| `src/data_curation/` | `module_data.py`, `module12_augmentation.py`, `module12b_new_added_data.py`, `module13_merge_three_sources.py`, `module14a_export_for_cdhit.py`, `module14b_parse_clusters.py`, `module14c_final_clusters.py`, `check_short_seqs.py`, `verify_cluster_ids.py` |
| `src/classifiers/` | `module2_classifier.py`, `module2_classifier_v3.py`, `module2_classifier_v4.py`, `module_tox1_export_for_cdhit.py`, `module_tox2_rebuild_classifier.py`, `module_tox3_reconcile_signals.py` |
| `src/generative_models/` | `module3_vae.py`, `module4_transformer.py`, `module4_preflight_check.py`, `module5_vae_retrain.py`, `module_vae_diag_diversity.py`, `quick_diversity_check.py`, `check_diversity.py`, `vae_training_1.py`, `transformer1.py` |
| `src/optimization/` | `module10_multiobjective.py`, `module10_multiobjective_v2.py`, `module10_multiobjective_v3.py` (**v3 is current — say so in each file's docstring if it isn't already clear**) |
| `src/structure_prediction/` | `module7_structures.py`, `select_esmfold_candidates.py`, `filter_for_esmfold.py`, `check_diversity.py`, `helix_wheel.py`, `module6_score_candidates.py`, `module6_umap.py` |
| `src/md_simulation/` | `run_md.py`, `run_md_remaining.py`, `extend_md.py`, `analyze_md.py`, `analyze_contacts.py`, `analyze_insertion.py`, `analyze_engaged_fraction.py`, `analyze_all_remaining.py`, `lipid_perturbation.py`, `tilt_windowed.py`, `check_orientation.py`, `check_resnames.py`, `check_transition.py`, `extract_snapshots.py`, `rebuild_trajectories.py`, `normalize_contacts.py` |
| `notebooks/` | `amp_md_pilot_fresh.ipynb` |
| `results/` | `pareto_candidates_v3.csv`, `all_candidates_scored_v3.csv`, `vae_v3_candidates_filtered.csv`, `transformer_v5_generated_filtered.csv` |
| `archive/phase1_rl_esm2/` | `module9_rl.py`, `FINAL_CANDIDATES.csv`, `e250_*.csv`, `transformer_v2_e250_oracle_scores.csv` |
| `models/` (LFS) | `amp_classifier_v5.pkl`, `toxicity_classifier_v2.pkl`, `vae_model_v3_fixed.pth` — **only after `git lfs track` below** |
| `data/raw/` | **Do not add yet** — see `DATA_SOURCES.md` first. |

Two things I didn't map, on purpose: `module11_final_pipeline.py` and
`module14b_parse_clusters.py`'s superseded sibling — check which of
`module14b_parse_clusters.py` vs `module14c_final_clusters.py` is actually
current (v14c handles short-sequence exact-match grouping that v14b doesn't)
before deciding whether to keep both or archive v14b too.

## 4. Track large files with LFS before adding them

```bash
git lfs track "*.pkl"
git lfs track "*.pth"
git add .gitattributes
git add models/
git commit -m "Add model weights via Git LFS"
```

## 5. Create the GitHub repo and push

Create an empty repo on github.com first (no README/license/gitignore —
you already have those), then:

```bash
git remote add origin https://github.com/<your-username>/amp-generative-pipeline.git
git branch -M main
git push -u origin main
```

## Before you make it public

- [ ] Confirm with Dr. Aswathy U / Dr. Nidhin S / Accubits that public
      release is fine.
- [ ] Resolve the APD6/dbAMP3 redistribution-terms checklist item in
      `DATA_SOURCES.md` before adding anything to `data/raw/`.
- [ ] Decide whether `module14b_parse_clusters.py` should move to `archive/`
      alongside its superseded status, per the note above.
- [ ] Skim every script for hardcoded local paths (`C:\Users\arjun\...`,
      `/content/drive/...`) — these won't break anything but are a minor
      tell that the repo wasn't cleaned up before publishing.
