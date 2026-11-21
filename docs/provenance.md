# Provenance and release review

## Source and attribution

The review covers the [team's Drone Flyby history](https://github.com/rabrie10/Gforce-ai-cup-2026/commits/main/drone-flyby) at main commit `9dc81a16784999ebbf320467587b1115ce0aab57` and its related remote branches. Git records Mahdi Nasser as the primary author of the Drone Flyby implementation and experiments and @rabrie10 as a contributor to the starter integration and repository cleanup. Git also records Copilot scaffolding and Claude trailers. Ambolt AI authored the competition materials. The team repository is the source of record for authorship of shared work.

## Proposed source layout

The extraction flattens `drone-flyby/` to the root while keeping module paths intact. `v2/` and `v5/` hold the inference lineage, `architecture_experiments/` holds experiment code, and `tests/` holds regression tests. The other two challenges are outside the path allowlist.

The local dry run retained 36 commits for 117 Python files from `main`. The selected paths' commits were authored by Mahdi. The starter integration and scaffolding contain files outside that selection and remain credited in the README. The unmerged V4 branch remains available in the team repository. Git filtering retains author metadata and commit messages for surviving commits and assigns new commit IDs. This operation took place in a separate clone.

The [source path allowlist](source-paths.txt) pins the dry-run selection. To reproduce it locally in a fresh, disposable clone, check that `main` points to the revision above and run Git Filter Repo 2.47.0:

```bash
git filter-repo \
  --paths-from-file /absolute/path/to/source-paths.txt \
  --path-rename drone-flyby/:
```

Use a single-branch, no-tags clone for this operation. `git fsck`, syntax parsing on all 117 files and the path allowlist across all 36 commits passed. The 139 reachable historical blob versions were Python files and the largest was about 62 KiB. The staged source needs organizer modules such as `dtos.py` and `utils.py` supplied separately to run. The standalone GitHub repo currently holds the review documentation.

## What needs clearance

| Material | Finding | Decision |
| --- | --- | --- |
| Team implementation and Git history | The team repo has no visible license. The [competition terms, sections 8–9](https://nordicaicup.com/terms) leave future use and credit to the team and require third-party compliance. | Obtain agreement from the team and any applicable rights holders on the files, history, attribution, and license for a standalone release. |
| Organizer starter code and evaluator | The organizer repo has no visible license. The two trees share 63 paths, 57 of them byte-identical at the reviewed tips, including `dtos.py`, `utils.py`, and `local_evaluator.py`. | Keep starter files external unless Ambolt AI confirms redistribution terms. Review any remaining adaptations before release. |
| Images, labels, composites, and captures | The starter includes Helsinki frames and annotations. Hosted captures, image overlays, per-frame outputs, and training composites can contain or derive from competition material. | Keep these out of every published revision unless the applicable terms explicitly cover the exact material. The README diagram uses original text and contains no competition imagery. |
| Weights and dependencies | The team history includes baseline `.pt`, `.onnx`, and `.npz` files. The final V7 weights and heads are archived outside Git. [Ultralytics](https://docs.ultralytics.com/#yolo-licenses-how-is-ultralytics-yolo-licensed) offers AGPL-3.0 and Enterprise terms. [Meta's DINOv2 upstream](https://github.com/facebookresearch/dinov2#license) states Apache-2.0 for its own code and weights. | Keep model artifacts out. Verify the terms for the exact YOLO integration, exports, and fitted checkpoints, plus rights in the underlying training material, before selecting a release license. |

The published [GitHub licensing guidance](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/licensing-a-repository) distinguishes viewing and forking from permission for a separate redistribution. No license was assigned to this review.

Before any public source release, recheck all reachable commits for copied code, sensitive operational details, and restricted assets. Confirm what may be shared in aggregate and keep the official-result provenance with the README.
