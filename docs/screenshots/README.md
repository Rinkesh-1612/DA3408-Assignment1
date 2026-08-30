# Screenshots

Evidence captured from the MLflow UI.

| File | What it shows | Question |
|---|---|---|
| `q2_run_comparison.jpeg` | The `mnist-mlp-experiments` Runs table with all six runs, sorted by `val_accuracy`, with `train_loss` alongside | Q2 |
| `q2_run_comparison_detail.png` | The Compare view of the same six runs, showing every logged parameter and metric side by side | Q2 |
| `q4_run_details.jpeg` | `q4-partner-a-run`: params (`n_estimators`, `max_depth`, `seed=42`), metrics (`val_accuracy`, `val_f1_macro`) and the `git_commit` tag | Q4 |
| `q4_model_artifact.jpeg` | The logged model artifact, showing it was logged from `q4_train_and_register.py` and registered as `q4-digits-classifier v1` | Q4 |
| `q4_model_staging.jpeg` | `q4-digits-classifier` Version 1 with Stage = **Staging** | Q4 |

The Q3 rollback proof is terminal output rather than a screenshot; it lives in
`docs/evidence/q3_rollback.txt`.

Note: MLflow 3.x labels the stage field "Stage (deprecated)" because stages are being
replaced by aliases. The assignment asks for the stages API, which is what was used.
