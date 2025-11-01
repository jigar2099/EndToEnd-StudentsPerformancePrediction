# StudentsPerformancePrediction

Predicting student performance from tabular data — built as a small, end‑to‑end ML project.

Last updated: 2025-11-01

## What this is
- A simple, reproducible pipeline for: data ingestion → data transformation → model training → evaluation.
- Status: the core components are implemented and runnable from the `data_ingestion` module’s `__main__` block. A separate CLI pipeline is still a TODO.
- There’s an EDA notebook under `notebook/` you can open to get a feel for the data and possible features.

## Stack (short and sweet)
- Python (version not pinned yet)
- pip + setuptools (`setup.py` with `find_packages`)
- Key libraries: `pandas`, `numpy`, `scikit-learn`, `xgboost`, `catboost`, `seaborn` (see `requirements.txt`)
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

2) Install dependencies
```bash
pip install -r requirements.txt
pip install -e .
```

## Run the implemented pipeline (ingestion → transformation → training)
Right now the quickest way to run everything end‑to‑end is to execute the `data_ingestion` module directly. Its `__main__` block chains the three steps and prints the final R² on the test split.

- From the project root (StudentsPerformancePrediction):
  - Windows (PowerShell):
    ```powershell
    python -m src.components.data_ingestion
    ```
  - Or directly as a script:
    ```powershell
    python .\src\components\data_ingestion.py
    ```
  - macOS/Linux:
    ```bash
    python -m src.components.data_ingestion
    # or
    python src/components/data_ingestion.py
    ```

What it does under the hood:
1) Data ingestion (`src/components/data_ingestion.py`)
   - Reads the Students Performance CSV from:
     `C:\Users\jigar\Desktop\EndToEnd\StudentsPerformancePrediction\notebook\data\stud.csv`
   - Splits into train/test with `test_size=0.2`, `random_state=42`.
   - Writes CSVs under an artifacts folder anchored to `src/`:
     - `src/artifacts/raw.csv`
     - `src/artifacts/train.csv`
     - `src/artifacts/test.csv`

2) Data transformation (`src/components/data_transformation.py`)
   - Target: `math_score`.
   - Numerical features: `writing_score`, `reading_score`.
   - Categorical features: `gender`, `race_ethnicity`, `parental_level_of_education`, `lunch`, `test_preparation_course`.
   - Pipelines:
     - Numerical: `SimpleImputer(strategy="median")` → `StandardScaler()`
     - Categorical: `SimpleImputer(strategy="most_frequent")` → `OneHotEncoder()` → `StandardScaler(with_mean=False)`
   - Uses a `ColumnTransformer` to combine both.
   - Saves the fitted preprocessor to: `src/artifacts/preprocessor.pkl`.
   - Returns NumPy arrays for train and test where the last column is the target.

3) Model training (`src/components/model_trainer.py`)
   - Splits the arrays back into `X` and `y`.
   - Trains and evaluates several regressors with simple hyper‑parameter searches via `GridSearchCV` (see `src/utils.py::evaluate_models`):
     - RandomForestRegressor, DecisionTreeRegressor, GradientBoostingRegressor, LinearRegression, XGBRegressor, CatBoostRegressor, AdaBoostRegressor
   - Selects the model with the best test R², requires R² ≥ 0.6, and saves it to: `src/artifacts/model.pkl`.
   - Prints/returns the final test R².

## Where outputs are saved
- Artifacts directory is anchored to the `src/` folder (not the current working directory):
  - `src/artifacts/raw.csv`
  - `src/artifacts/train.csv`
  - `src/artifacts/test.csv`
  - `src/artifacts/preprocessor.pkl`
  - `src/artifacts/model.pkl`

## Logging
- `src/logger.py` writes timestamped logs under `src/logs/` (e.g., `src/logs/11_01_2025_20_16_20.log`).

## Notes and limitations
- The ingestion path to the CSV is currently hard‑coded. If you’re running on another machine, update the path in `src/components/data_ingestion.py` or place the CSV in the same location.
- The end‑to‑end execution is provided via the `data_ingestion` module’s `__main__` block for now. A proper CLI (`src/pipeline/train_pipeline.py`, `predict_pipeline.py`) is still a TODO.

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

## Scripts / entry points
- Configured as a Python package via `setup.py`.
- No `console_scripts` yet. Once the CLIs are added, we can wire them up here or use `python -m ...`.

## Config and environment
- No required env vars right now.
- A future improvement is to add `configs/*.yaml` and pass paths/params from the CLI rather than hard‑coding them.

## Tests
- None yet. Recommended: add `pytest` with tests for:
  - ingestion (paths and splits)
  - transformations/feature engineering (preprocessor persistence)
  - training + evaluation (model selection, R² threshold)
  - prediction pipeline and error handling

## Project layout
```
StudentsPerformancePrediction/
├─ README.md
├─ requirements.txt
├─ setup.py
├─ notebook/
│  └─ 1 . EDA STUDENT PERFORMANCE .ipynb
└─ src/
   ├─ __init__.py                
   ├─ logger.py                  # logs to src/logs
   ├─ exception.py               
   ├─ utils.py                   # save_object, evaluate_models, load_object
   ├─ artifacts/                 # generated outputs (created at runtime)
   │  ├─ raw.csv
   │  ├─ train.csv
   │  ├─ test.csv
   │  ├─ preprocessor.pkl
   │  └─ model.pkl
   ├─ components/
   │  ├─ data_ingestion.py       # implemented
   │  ├─ data_transformation.py  # implemented
   │  └─ model_trainer.py        # implemented
   └─ pipeline/
      ├─ train_pipeline.py       # TODO
      └─ predict_pipeline.py     # TODO
```

## Data
- Source: Kaggle “Students Performance” dataset (CSV). For now, the repository expects the CSV at the hard‑coded path shown above.

## Dev notes
- `requirements.txt` includes `-e .`. `setup.py` filters it out for `install_requires`, so it’s safe to keep when installing via `-r`.
- Consider pinning a minimum Python version in `setup.py` or adding a `pyproject.toml`.
- Tooling you might want: `black`, `ruff`/`flake8`, `mypy`, `pre-commit`.

## Contributing
PRs are welcome. Next steps that would add the most value: parameterize input paths, implement CLI pipelines, and add tests.

## License
TODO — add a `LICENSE` file and note the chosen license here (e.g., MIT, Apache‑2.0).