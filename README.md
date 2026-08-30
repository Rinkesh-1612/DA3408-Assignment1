# DA3408 — AI Operations, Module 1 Assignment

Experiment Management & Reproducibility. This repository holds my complete
submission for all four questions.

The one-page report with the written answers is **`DA3408_Assignment_1.pdf`**. Everything
below tells you where each artefact lives and how to run the code yourself.

Most of this builds on the slides and the exercise notebooks Sir shared in his repo. The
Q2 training script started from the Lecture 3 exercise and I adapted it to an MLP on
MNIST, and the DVC and MLflow workflows follow what we covered in class. AI tool usage
is declared in `AI_DISCLOSURE.md`.

---

## Where to find each answer

| Question | What it asks for | Where it is |
|---|---|---|
| **Q1** — Technical debt diagnosis | Written answer only | `DA3408_Assignment_1.pdf`, section *Q1* |
| **Q2** — MLflow experiment comparison | 6 runs, comparison screenshot, analysis, logging code | Notebook: `notebooks/Exercise2_Package_and_Run_MLflow_Project.ipynb`<br>Screenshot: `docs/screenshots/q2_run_comparison.jpeg`<br>Analysis + code listing: `DA3408_Assignment_1.pdf`, section *Q2* |
| **Q3** — DVC versioning & rollback | v1/v2 on an SSH remote, rollback proof | Data pointer: `data.csv.dvc` (tags `v1`, `v2`)<br>Remote config: `.dvc/config`<br>Terminal proof: `docs/evidence/q3_rollback.txt`<br>Write-up: `DA3408_Assignment_1.pdf`, section *Q3* |
| **Q4** — End-to-end reproducibility drill | Partner A/B drill, model registered to Staging | Code: `src/q4_train_and_register.py`, `src/prepare_q4_data.py`<br>Data pointer: `data/q4_digits.csv.dvc`<br>Environment: `environment.yml`<br>**Shared partner repo:** https://github.com/Rinkesh-1612/DA3408-Assignment1-Q4<br>Write-up: `DA3408_Assignment_1.pdf`, section *Q4* |

> **Q4 note.** Per the TA's clarification, the Partner A / Partner B collaboration is done
> in a **separate shared repository** (linked in the table above) so that each partner's
> contribution is visible as its own commit. That repo is private and my partner is a
> collaborator on it. The Q4 code and artefacts are also mirrored here so this repository
> remains a complete, self-contained submission.

---

## Setup

```bash
mamba env create -f environment.yml     # or: conda env create -f environment.yml
conda activate da3408-assignment1
```

Pinned versions live in `environment.yml` (Python 3.12, `mlflow==3.15.1`,
`scikit-learn==1.9.0`, `pandas==2.3.3`, `numpy==2.5.2`, `dvc==3.67.1`, `dvc-ssh`).
Pinning them is what makes the Q4 drill reproducible on a different machine.

### Starting the MLflow tracking server

```bash
mlflow server \
  --backend-store-uri sqlite:///mlflow.db \
  --default-artifact-root ./mlruns \
  --host 0.0.0.0 --port 5000 \
  --allowed-hosts "<server-ip>:5000,localhost:5000" \
  --cors-allowed-origins "http://<server-ip>:5000,http://localhost:5000"
```

The last two flags matter: MLflow 3.x rejects Host headers and cross-origin requests it
does not recognise, so without them the UI loads but every API call it makes returns
403 and the page shows *Request error / INTERNAL\_ERROR*. Drop the `--allowed-hosts` /
`--cors-allowed-origins` pair if you are only ever browsing from `localhost`.

---

## Reproducing each question

### Q2 — six MLflow runs on MNIST

Open `notebooks/Exercise2_Package_and_Run_MLflow_Project.ipynb` and run it top to
bottom. It trains an MLP on MNIST across six hyperparameter configurations and logs
params, metrics, tags and the model artifact for each.

Note that re-running the notebook **appends** six more runs rather than replacing the
existing ones, so delete the old runs first if you want a clean comparison table.

### Q3 — DVC rollback

`data.csv` is DVC-tracked, with the two versions tagged in git:

```bash
git checkout v1 -- data.csv.dvc && dvc checkout && wc -l data.csv   # 1801 = 1800 rows + header
git checkout v2 -- data.csv.dvc && dvc checkout && wc -l data.csv   # 2801
```

Captured output of exactly this is in `docs/evidence/q3_rollback.txt`.

The DVC remote is SSH (`.dvc/config`). It points at this project's own host, so a
`dvc pull` will only succeed with access to that machine — the rollback proof above is
the graded artefact, and it does not require the remote.

### Q4 — the reproducibility drill

```bash
python src/prepare_q4_data.py                  # regenerates data/q4_digits.csv
python src/q4_train_and_register.py            # trains, logs, registers, promotes to Staging
```

`src/q4_train_and_register.py` logs the params, metrics, the `seed`, a `git_commit` tag
and the model artifact, then registers the model as `q4-digits-classifier` and
transitions version 1 to **Staging**. Point `MLFLOW_TRACKING_URI` at your tracking
server before running it.

The dataset is derived deterministically from scikit-learn's bundled `load_digits`, so
`prepare_q4_data.py` reproduces it byte-for-byte and `dvc status` will confirm the hash
matches `data/q4_digits.csv.dvc`.

---

## Demo video

https://drive.google.com/file/d/1E-D-WWa3JvGlkzXNSYUsB44WWhJL_kR6/view?usp=drive_link

Shared publicly with link access, so no permission request is needed. It walks through
all four questions: the technical debt mapping, the six MLflow runs, a live DVC rollback
from v2 back to v1, and the Q4 drill with my partner.

## Repository layout

```
.
├── notebooks/   Q2 MLflow experiment notebook
├── src/         Q4 data prep + train/register scripts
├── data/        Q4 DVC-tracked dataset pointer
├── docs/        assignment brief, evidence, screenshots
├── data.csv.dvc Q3 DVC-tracked dataset pointer (tags v1 / v2)
├── environment.yml
└── DA3408_Assignment_1.pdf  the one-page report
```
