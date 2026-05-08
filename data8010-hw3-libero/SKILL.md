---
name: data8010-hw3-libero
description: Use for DATA8010 or similar LIBERO behavior-cloning homework projects when Codex needs to inspect LIBERO Spatial assets, train or evaluate multi-task BC baselines, structure a TeX report with Part 1, Part 2, Part 3, Bonus A, Bonus B, and reproducibility sections, package clean submission zips, or audit public-safe release artifacts.
---

# DATA8010 HW3 LIBERO

## Overview

Use this skill to run a reproducible LIBERO behavior-cloning homework workflow
without mixing experiment evidence, report claims, submission packages, and
public-release material.

The skill assumes an existing project repo with Python scripts for asset
inspection, BC training, rollout evaluation, TeX report generation, and
submission packaging. Adapt command names to the local repo when they differ.

## Entry Gate

Before editing, training, evaluating, rebuilding a report, or packaging:

```bash
pwd
git status --short --branch
pgrep -af "train_bc.py|eval_bc.py|watch_bc.py|run_.*\\.sh|bc_taskid|bonus_b" || true
find results -maxdepth 3 \( -name eval_results.json -o -name train_log.csv \) -printf '%T@ %p\n' | sort -nr | head -80
```

Then state:

- which repo or package is being changed
- current branch and dirty state
- whether training or evaluation is already running
- latest reliable result JSON files found

Never auto-switch branches, kill jobs, delete results, overwrite final
artifacts, commit, or push unless the user explicitly asks.

## Expected Project Shape

Prefer existing repo scripts and names. In many DATA8010 HW3 repos the important
paths look like:

- `scripts/doctor.py`, environment and LIBERO asset check
- `scripts/inspect_assets.py`, warm-up task inspection and demo-frame export
- `scripts/train_bc.py`, supervised multi-task BC training
- `scripts/eval_bc.py`, closed-loop rollout evaluation
- `scripts/plot_results.py`, training and success plots
- `scripts/build_report_tex.py`, official TeX report generator
- `scripts/package_submission.py`, code/report/video package builder
- `artifacts/inspection/inspection_summary.json`
- `artifacts/inspection/task_1_demo_frame.png`
- `artifacts/inspection/task_5_demo_frame.png`
- `results/**/train_log.csv`
- `results/**/eval_results.json`
- `report/DATA8010_HW3_Report.tex`
- `report/DATA8010_HW3_Report.pdf`
- `submission/DATA8010_HW3_Code.zip`
- `submission/DATA8010_HW3_Report.pdf`
- `submission/DATA8010_HW3_Videos.zip`

Treat generated result directories, checkpoints, PDFs, videos, submission zips,
and TeX logs as artifacts. Do not move them into a public skill repo.

## Evidence Rules

Use current JSON and CSV artifacts for claims. Do not rely on memory.

For each reported run, extract:

- task IDs and benchmark name
- rollout count and horizon
- average success
- per-task successes and rates
- checkpoint path or epoch
- final train and validation loss
- model changes, such as backbone, wrist view, frame stack, or GMM head

Prefer higher-rollout verification for final four-task claims. Treat small
checkpoint sweeps as selection evidence unless the report clearly labels them as
such.

Keep task scopes separate:

- four-task core or Bonus A results compare only the same four tasks
- all-task Bonus B results are separate scaling evidence
- an all-task result should not replace a four-task result unless the assignment
  explicitly asks for that replacement

## Report Structure

For the official TeX report, use this section order exactly unless the assignment
prompt says otherwise:

1. `Part 1: Environment and Dataset Check`
2. `Part 2: Multi-task BC Baseline`
3. `Part 3: Controlled Comparison`
4. `Bonus A: Four-task Improvement`
5. `Bonus B: All-task Scaling`
6. `Required Questions and Reproducibility Checklist`

Part 1 should include the warm-up task inspection for tasks 1 and 5:

- language instruction
- BDDL path and demo path, preferably relative to the LIBERO checkout
- number and shape of benchmark initial states
- observation keys
- image shape
- action shape
- sample trajectory lengths
- saved demo-frame images when available

Part 2 should report the multi-task BC baseline:

- task set, normally `0, 1, 5, 6`
- model and conditioning method
- final train and validation losses
- average success
- per-task success for tasks `0`, `1`, `5`, and `6`

Part 3 should isolate the controlled comparison:

- conditioned versus no-task figure or table
- average and per-task comparison
- one short interpretation of whether conditioning helped

Bonus A should answer:

- what changed
- whether it helped
- why it likely helped or failed

Bonus B should separately report all-task scaling:

- all task IDs
- per-task success counts
- average success
- routing or checkpoint policy when used
- explicit note that this is separate from the four-task result

Keep the report within the assignment page limit by compacting tables, figures,
and wording. Do not drop required items to save space.

## Package Rules

The code zip should contain reusable source code and lightweight docs only. Keep
it free of:

- stale Markdown report sources
- generated PDFs
- videos
- checkpoints
- result directories
- TeX logs and aux files
- campaign logs or private assistant artifacts
- local absolute paths and identity-bearing public-release material

The optional video zip should contain MP4 files only plus `README.md`. Do not
ship `README.txt` or WebM fallbacks unless the assignment explicitly requests
them.

Run the package script after rebuilding the PDF, then inspect both zip files
before submission.

## Validation

Use the local Python environment selected by the project. If the repo provides a
wrapper script, use it.

Core checks:

```bash
python -m py_compile scripts/train_bc.py src/libero_bc_common.py scripts/eval_bc.py scripts/build_report_tex.py scripts/package_submission.py
python scripts/build_report_tex.py
pdfinfo report/DATA8010_HW3_Report.pdf
pdftotext report/DATA8010_HW3_Report.pdf -
python scripts/package_submission.py
unzip -t submission/DATA8010_HW3_Code.zip
unzip -t submission/DATA8010_HW3_Videos.zip
zipinfo -1 submission/DATA8010_HW3_Videos.zip
```

Report checks:

```bash
rg -n "Part 1: Environment and Dataset Check|Part 2: Multi-task BC Baseline|Part 3: Controlled Comparison|Bonus A: Four-task Improvement|Bonus B: All-task Scaling|Required Questions and Reproducibility Checklist" report/DATA8010_HW3_Report.tex
rg -n "README\\.txt|DATA8010_HW3_Report\\.md" submission || true
```

Public-release privacy check:

```bash
rg -n "absolute-home-path|student-identifier-pattern|known-person-name|private-repo-slug|assistant-artifact-dir" .
```

Replace the placeholders with the names, IDs, local path fragments, and private
repo slugs that matter for the current workspace. Course reports may require
identity fields in the submission workspace, but public skill repositories
should not include them.

## Public Skill Boundary

When preparing a public Codex App skill repo:

- include only the installable skill directory, repo-level README, changelog, and
  license
- keep the skill reusable, not tied to one student's workspace
- scrub personal names, identifier numbers, absolute paths, private repo names,
  reference-manager paths, private memory references, and generated submission
  artifacts
- include `agents/openai.yaml` for Codex App metadata
- avoid non-Codex plugin files when the requested surface is Codex App only
- validate with `quick_validate.py`

The public skill may mention the course and LIBERO workflow. It should not ship
course PDFs, datasets, checkpoints, rollouts, reports, zips, private notes, or
machine-specific paths.
