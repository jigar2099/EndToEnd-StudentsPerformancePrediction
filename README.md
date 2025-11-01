# StudentsPerformancePrediction

Predicting student performance from tabular data — built as a small, end‑to‑end ML project. The repo has the basics in place (logging, exceptions, a few components, an EDA notebook) and leaves clear TODOs for the rest.

Last updated: 2025-11-01

## What this is
- A simple, reproducible pipeline for: ingesting data → transforming it → training a model → making predictions.
- Current status: early scaffolding. Several files exist as placeholders and still need real code.
- There’s an EDA notebook under `notebook/` you can open to get a feel for the data and possible features.

## Stack (short and sweet)
- Python (version not pinned yet)
- pip + setuptools (`setup.py` with `find_packages`)
- Requirements: `pandas`, `numpy`, `seaborn` (see `requirements.txt`)
- Standard `src/` package layout, supports editable installs via `-e .`

## Quick start
1) Create a virtual environment (recommended)
- Windows (PowerShell):
  ```powershell
  python -m venv .venv
  .\.venv\Scripts\Activate.ps1
  ```
- macOS/Linux:
  ```bash
  python3 -m venv .venv
  source .venv/bin/activate
  ```

2) Install deps (editable install works):
```bash
pip install -r requirements.txt
pip install -e .
```

## Run things (what works today)
End‑to‑end train/predict CLIs are not implemented yet. These files are present but need code:
- `src/pipeline/train_pipeline.py` — training entry point (TODO)
- `src/pipeline/predict_pipeline.py` — prediction entry point (TODO)
- `src/components/data_ingestion.py` — bring in raw/processed data (TODO)
- `src/components/data_transformation.py` — preprocessing/feature engineering (TODO)
- `src/components/model_trainer.py` — training + saving the model (TODO)
- `src/utils.py` — common helpers (I/O, metrics, etc.) (TODO)

While those are WIP, you can open the EDA notebook:
```bash
jupyter notebook "notebook/1 . EDA STUDENT PERFORMANCE .ipynb"
```

## EDA highlights (from the notebook)
These are the key takeaways from `notebook/1 . EDA STUDENT PERFORMANCE .ipynb` (Kaggle Students Performance dataset, 1,000 rows, 8 columns):

- Data health
  - No missing values or duplicate rows were found.
  - Numeric summaries: means cluster around 66–68 with std ~14–15. Math has outliers down to 0; reading/writing have higher minimums (10–17).
- Score distributions
  - Most students score between 60–80 in all subjects; reading/writing distributions are slightly stronger than math.
  - Subject scores are positively correlated; higher in one subject tends to coincide with higher in the others.
- Gender
  - Females have a higher overall average and tend to do better in reading and writing.
  - Males slightly edge females in math on average.
- Lunch type
  - Students with standard lunch score higher across subjects than those with free/reduced lunch.
- Test preparation course
  - Students who completed the prep course score higher in math, reading, and writing compared to those who did not.
- Race/Ethnicity
  - Group E has the highest average scores; Group A the lowest. Groups C/D are the most represented.
- Parental education
  - Higher parental education (bachelor’s/master’s) is associated with slightly higher student scores overall. Effects vary by gender and are not uniformly strong.

Caveats: These are observational correlations from EDA and do not imply causation. They should guide feature engineering and hypothesis formation, and be validated via modeling and, where possible, controlled studies.

When the pipelines exist, commands will look roughly like:
```bash
# placeholders — implement args/logic first
python -m src.pipeline.train_pipeline --config configs/train.yaml
python -m src.pipeline.predict_pipeline --model artifacts/model.pkl --input data/test.csv --output predictions.csv
```

## Scripts / entry points
- Configured as a Python package via `setup.py`.
- No `console_scripts` yet. Once the CLIs are added, we can wire them up here or use `python -m ...`.

## Config and environment
- No required env vars right now.
- There’s no config system yet. TODO: add something like `configs/*.yaml` and load it in the pipelines.

## Logging
- `src/logger.py` writes timestamped logs under `./logs/<timestamp>/<timestamp>.log` relative to the current working directory.

## Tests
- None yet. Recommended: add `pytest` with tests for:
  - ingestion
  - transformations/feature engineering
  - training + evaluation
  - prediction pipeline and error handling

## Where things live
```
StudentsPerformancePrediction/
├─ README.md
├─ requirements.txt
├─ setup.py
├─ notebook/
│  └─ 1 . EDA STUDENT PERFORMANCE .ipynb
└─ src/
   ├─ __init__.py                # ensure package marker exists (TODO if missing)
   ├─ logger.py                  # timestamped logs in ./logs
   ├─ exception.py               # simple exception helper
   ├─ utils.py                   # shared helpers (TODO)
   ├─ components/
   │  ├─ data_ingestion.py       # TODO
   │  └─ data_transformation.py  # TODO
   └─ pipeline/
      ├─ train_pipeline.py       # TODO
      └─ predict_pipeline.py     # TODO
```
Note: some files above are empty or partial and will need implementation.

## Data
- Dataset paths/download are not set yet. TODO: document where the data comes from, expected schema, and how to access it (local path, URL, registry, etc.).

## Model artifacts
- Not implemented. TODO: decide an `artifacts/` layout (models, transformers, metrics) and document how they’re saved/loaded.

## Dev notes
- `requirements.txt` includes `-e .`. `setup.py` filters it out for `install_requires`, so it’s safe to keep when installing via `-r`.
- Consider pinning a minimum Python version in `setup.py` or adding a `pyproject.toml`.
- Tooling you might want: `black`, `ruff`/`flake8`, `mypy`, `pre-commit`.

## Contributing
PRs are welcome. The biggest wins right now: implement the pipeline modules, add tests, and set up configuration/data management.

## License
TODO — add a `LICENSE` file and note the chosen license here (e.g., MIT, Apache‑2.0).