# AI Disclosure

## Tools used

Claude Code (Anthropic).

## How I used it

**Formatting and writing up.** My write-up had way more content than would fit on one
page, so I used it to condense the LaTeX down while keeping all four answers intact and
to tidy up the wording. Same for the README. I knew what I wanted in there and used it to
structure the question-to-file index and get the setup instructions readable.

**SSH and GitHub setup.** Getting the SSH keypair working for the DVC remote, and later
sorting out keys and tokens so I could push to GitHub and add my partner as a
collaborator on the Q4 repo.

**Working out the DVC remote for Q4.** This took a fair bit of trial and error. I went
back and forth on S3 and S3-compatible options like MinIO before realising the whole
approach was wrong for this. Anything hosted on my own server would need that server
running and credentials shared at evaluation time. I ended up committing the DVC store
inside the repo instead so that `dvc checkout` works offline for anyone who clones it. I
used the tool to think through those trade-offs and test each option.

**Debugging.** Two things ate up time. MLflow 3.x rejects non-local Host headers by
default, so the UI loaded but every API call it made returned 403. That got fixed with
the `--allowed-hosts` and `--cors-allowed-origins` flags. The other one was my Q4
instructions saying `dvc pull` when the assignment specifies `dvc checkout`. My partner
flagged it and the fix was pointing DVC's cache at the in-repo store so that `dvc
checkout` works on a fresh clone.

**Boilerplate for Q4.** I used it to generate the first version of
`src/prepare_q4_data.py` and `src/q4_train_and_register.py`, meaning the standard MLflow
logging, model registration and stage transition calls. I picked the approach myself, a
small deterministic dataset with a fixed seed and a `git_commit` tag for traceability,
and reviewed and adjusted the code after.

## Impact

It sped up the writing and the debugging a lot and saved me from shipping a Q4 setup that
would have broken the moment my server went down. The design decisions were mine,
including what to log, how to split the two repos, and using an SSH remote for Q3 but a
self-contained store for Q4. I can explain and modify everything in this submission.
