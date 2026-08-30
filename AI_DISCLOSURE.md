# AI Disclosure

## Tools used

Claude Code (Anthropic).

## How I used it

**Formatting and writing up.** My write-up had way more content than would fit on one
page, so I used it to condense the LaTeX down while keeping all four answers intact, and
to tidy up the wording. Same for the README — I knew what I wanted in there, and used it
to structure the question-to-file index and get the setup instructions readable.

**SSH and GitHub setup.** Getting the SSH keypair working for the DVC remote, and later
getting keys and tokens sorted for pushing to GitHub and adding my partner as a
collaborator on the Q4 repo.

**Working out the DVC remote for Q4.** This took a fair bit of trial and error. I went
back and forth on S3 and S3-compatible options like MinIO before realising the whole
approach was wrong for this — anything hosted on my server would need the server running
and credentials shared at evaluation time. I ended up committing the DVC store inside
the repo instead, so `dvc checkout` works offline for anyone who clones it. I used the
tool to think through those trade-offs and test each option.

**Debugging.** Two things ate time. MLflow 3.x rejects non-local Host headers by
default, so the UI loaded but every API call returned 403 — fixed with `--allowed-hosts`
and `--cors-allowed-origins`. And my Q4 instructions originally said `dvc pull` when the
assignment specifies `dvc checkout`; my partner flagged it, and the fix was pointing
DVC's cache at the in-repo store so `dvc checkout` works on a fresh clone.

**Boilerplate for Q4.** I used it to generate the initial version of
`src/prepare_q4_data.py` and `src/q4_train_and_register.py` — the standard MLflow
logging, model registration and stage-transition calls. I chose the approach (small
deterministic dataset, fixed seed, `git_commit` tag for traceability) and reviewed and
adjusted the code.

## Impact

It sped up the writing and the debugging a lot, and saved me from shipping a Q4 setup
that would have broken the moment my server went down. The design decisions — what to
log, how to structure the repos, using an SSH remote for Q3 and a self-contained store
for Q4 — were mine, and I can explain and modify everything in this submission.
