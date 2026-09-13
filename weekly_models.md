# Weekly Models Overview

The notebooks for each week have been executed locally, so the output cells are saved in the files. When you push this repository to GitHub, the outputs will be displayed inline in the notebook view.

| Week | Notebook | Description |
|------|----------|-------------|
| 1 | [w01_research_question.ipynb](../w01_research_question.ipynb) | Exploration of the data and initial insights |
| 2 | [w02_ml_task_framing.ipynb](../w02_ml_task_framing.ipynb) | Define the ML task and framing |
| 3 | [w03_data_contract.ipynb](../w03_data_contract.ipynb) | Data contract and cleaning |
| 3 | [w03_feature_leakage_check.ipynb](../w03_feature_leakage_check.ipynb) | Check for feature leakage |
| 4 | [w04_baseline_score.ipynb](../w04_baseline_score.ipynb) | Baseline hand‑rule scoring |
| 4 | [w04_signal_audit.ipynb](../w04_signal_audit.ipynb) | Signal audit and analysis |
| 5 | [w05_model.ipynb](../w05_model.ipynb) | Train and evaluate models (logistic regression, decision tree, random forest) |
| 6 | [w06_validation_audit.ipynb](../w06_validation_audit.ipynb) | Validation set audit and performance analysis |
| 7 | [w07_action_playbook.ipynb](../w07_action_playbook.ipynb) | Final action playbook and recommendations |

**How to view the outputs on GitHub**
1. Commit and push the repository (including the updated notebooks) to your GitHub fork.
2. Navigate to each notebook file on GitHub – the output cells (tables, charts, print statements) will be rendered automatically.

**Optional helper script** – you can re‑run any week locally with:
```bash
jupyter nbconvert --to notebook --execute --inplace --ExecutePreprocessor.timeout=300 <notebook>.ipynb
```
Replace `<notebook>` with the desired week file.
