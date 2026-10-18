# EPTC 2026: TIM optimisation

Our team's entry for the EPTC 2026 thermal interface material (TIM) challenge. We look for the TIM-1 and TIM-2 designs that best balance chip temperature and TIM stress in a 65 × 65 mm, 400 W flip-chip package. The approach: Ansys runs, a surrogate model trained on them, and an optimiser that searches the surrogate.

Report and slides are due **Sun 1 Nov 2026**. The plan and shared checklist: https://claude.ai/artifact/9zo1yB99V7NLvEt7zvfHVq

## What is in this repo

| Folder | What goes there |
|---|---|
| `model/` | `eptc.wbpz`, the Ansys Workbench project archive. The one shared copy of the model |
| `cad/` | `EPTC_package.SLDPRT`, the SolidWorks part (7 bodies). Edit here when the geometry changes, for example TIM thickness |
| `automation/` | The Workbench batch script (`.wbjn`) and the Python runner that feeds it |
| `data/inputs/` | `inputs.csv` files: one row per design to run |
| `data/outputs/` | `outputs.csv` files that Ansys writes back, one row per design |
| `ml/` | Surrogate model, optimisation and plots |
| `materials/` | TIM property ranges and the table of real products |
| `docs/` | Challenge Guide, organiser slides, reference paper (Hu et al., EPTC 2019) |
| `report/` | Figures, report and slides |

**Not stored here:** the unpacked Workbench project (`.wbpj` plus its `_files` folder) and solver scratch files. They are large, tied to one computer's paths and rebuilt from the archive, so `.gitignore` keeps them out.

## One-time setup

1. Install Git for Windows (https://git-scm.com). Git LFS comes with it.
2. In a terminal, run `git lfs install` once.
3. Clone the repo, then run `git lfs pull` to download the large files.
4. For the Python parts: `python -m venv .venv`, activate it, then `pip install -r requirements.txt`.

## Editing the Ansys model

Git can store and share the model and keep every version. It can't merge two people's edits to the same Workbench file. So **one person edits the model at a time**, and the lock makes that visible.

1. `git pull`
2. `git lfs lock model/eptc.wbpz`. If someone else holds the lock, it says who. Wait, or ask them in the chat.
3. In Workbench: **File > Restore Archive**, pick `model/eptc.wbpz`, and restore into `work/` inside the repo (ignored by Git) or any folder outside it.
4. Make your change, solve, and check the results.
5. **File > Archive**, save over `model/eptc.wbpz`. In the options, tick *Imported files external to project directory*. Untick *Result/solution files* unless others need your results, which keeps the archive small.
6. `git add model/eptc.wbpz`, then `git commit -m "what you changed and why"`, then `git push`.
7. `git lfs unlock model/eptc.wbpz`
8. Post in the group chat what changed.

To only look at or run the model, skip the lock: pull, restore, run.

## Running a batch of designs

Batches run on any laptop with Ansys 2026 R1 and the hackathon licence:

1. Put the designs in `data/inputs/` as a CSV with the agreed columns (see the plan).
2. Run the Python runner in `automation/`. It calls Workbench in the background (`RunWB2.exe -B -R <script>.wbjn`), which updates each design point and writes a CSV to `data/outputs/`.
3. Commit the input and output CSVs together, so every result can be traced to its inputs.

One design takes about 4 minutes on a 4-core laptop (from the solve log), so 150 designs is about 10 hours. Run big batches overnight.

## Rules

- Never commit the unpacked project (`.wbpj`, `_files`). Always archive to `model/eptc.wbpz`.
- Lock before editing any file with `lockable` in `.gitattributes`, and unlock when done.
- Pull before you start, push when you finish. Small, frequent commits with clear messages.
- Everyone on Ansys 2026 R1. An archive saved in a newer version won't open in an older one.
- Each push of the archive uses Git LFS storage on GitHub (about 77 MB with results), so check the account's LFS quota and archive without results where you can.
