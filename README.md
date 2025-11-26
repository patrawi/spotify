# Spotify Music Popularity Prediction — Team 20



## Environment and Requirements
- Python: 3.10 or 3.11 (tested on 3.11)
- Core packages (tested versions):
  - numpy 1.26.x or 2.0+
  - pandas 2.2.x
  - scikit-learn 1.5.x
  - xgboost 2.0+ (2.1+ recommended)
  - matplotlib 3.8.x
  - seaborn 0.13.x
  - scipy 1.11+
  - jupyter, ipykernel

### requirements.txt (reference)
We recommend installing via a `requirements.txt`. If you don’t have one yet, you can create it with the following baseline (these bounds balance reproducibility and compatibility):

```
# requirements.txt
numpy>=1.26,<3.0
pandas>=2.1,<3.0
scikit-learn>=1.3,<2.0
xgboost>=2.0,<3.0
matplotlib>=3.7,<3.9
seaborn>=0.12,<0.14
scipy>=1.10,<2.0
jupyter>=1.0
ipykernel>=6.29
```

Install into a fresh virtual environment:

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
pip install -r requirements.txt
python -m ipykernel install --user --name spotify-aml --display-name "Python (spotify-aml)"
```

## Data Setup
Our analysis targets Spotify chart data (2017–2023). The repository currently includes processed CSVs used by the notebooks.

- If starting from raw data, place `Spotify_Dataset_V3.csv` at the repository root (or `data/raw/`) and update notebook paths if needed.
- If using processed data, ensure the CSVs present in this repo are accessible by the notebooks.

## How to Run
1. If starting from raw data, place `Spotify_Dataset_V3.csv` at the repository root (or `data/raw/`).
2. Run the notebooks in the Run Order below for cleaning, EDA, feature engineering, labeling, dimensionality reduction, clustering, and model training/tuning.
3. Ensure paths to CSVs in each notebook match your filesystem.

## Run Order (Notebooks)
The notebooks are numbered to suggest a logical pipeline. Depending on whether you start from raw or processed data, you may begin at different points.

1. `1_eda.ipynb` — Exploratory Data Analysis of raw/initial data; sanity checks and basic distributions.
2. `2_outlier_loudness.ipynb` — Detect outliers (e.g., loudness) and apply data quality fixes.
3. `3_duplicated.ipynb` — Identify and resolve duplicate records.
4. `4_datetime.ipynb` — Parse dates, normalize time fields, and build temporal features.
5. `5_loudness_normalization.ipynb` — Normalize loudness and verify consistency across subsets.
6. `6_EDA.ipynb` — Extended EDA on aggregated and split datasets (duration buckets, best-rank distribution, release timing, artist frequency, audio outliers/boxplots, correlations, and scatterplots).
7. `7_1_Artist_Collabs_Calcu.ipynb` — Compute artist-level statistics (song_count, avg_rank, best_rank) and collaborator features from training split; create lookup dictionaries.
8. `7_2_Artist_Collabs_Calcu_test.ipynb` — Build combined train+val lookups for test; save pickles (artist stats, top collaborators, popularity, global mean).
9. `8_1_Feature_Engineering.ipynb` — Construct feature sets (audio + reputation + temporal), merge lookups, and produce ML-ready tables.
10. `8_2_Feature_Engineering_test.ipynb` — Validate engineered features on hold-out splits; ensure schema integrity.
11. `9_Preprocessing_D_Reduction.ipynb` — Apply preprocessing (scaling/encoding) and dimensionality reduction (e.g., PCA) where appropriate.
12. `10_Model_Tuning_Test.ipynb` — Train/tune Random Forest, XGBoost, and NN variants with temporal splits; evaluate and compare metrics.
13. `11_cluster.ipynb` — (Suppliment Task) Perform clustering analyses to explore feature-space structure and support EDA conclusions.

Notes:
- The training notebooks implement temporal train/test splits to avoid leakage. Ensure date-based splits align with the target prediction horizon.
- For model comparison, notebooks train Random Forest, XGBoost, and simple neural networks, log metrics, and save artifacts.

## Reproducing Reported Results
To reproduce our submission results:

1. Use Python 3.11 and the package bounds in the provided `requirements.txt` reference.
2. Ensure you are using temporal splits (as configured in the training notebooks) — do not shuffle across time.
3. Set deterministic seeds where applicable:
	- scikit-learn models: set `random_state=42` (done in training notebooks where relevant).
	- XGBoost: set `random_state=42` and optionally `seed=42` for parity across versions.
4. Run the model notebooks in the order listed above. Primary results are produced in `10_Model_Tuning_Test.ipynb`; clustering insights are documented in `11_cluster.ipynb`.
5. Export or view the metrics tables and model comparison plots generated at the end of those notebooks.

If you encounter version-related differences, try using the exact versions listed under Environment and Requirements and re-run the pipeline. On Apple Silicon, ensure you are using wheels compatible with your Python version (pip will handle this for the listed packages).

## File Structure (overview)
```
.
├── 1_eda.ipynb
├── 2_outlier_loudness.ipynb
├── 3_duplicated.ipynb
├── 4_datetime.ipynb
├── 5_loudness_normalization.ipynb
├── 6_EDA.ipynb
├── 7_1_Artist_Collabs_Calcu.ipynb
├── 7_2_Artist_Collabs_Calcu_test.ipynb
├── 8_1_Feature_Engineering.ipynb
├── 8_2_Feature_Engineering_test.ipynb
├── 9_Preprocessing_D_Reduction.ipynb
├── 10_Model_Tuning_Test.ipynb
├── 11_cluster.ipynb

```

## Notebook Summaries
- `1_eda.ipynb`: Initial EDA, distributions, missingness, and basic correlations.
- `2_outlier_loudness.ipynb`: Detects/handles outliers (esp. loudness) and normalizes problematic features.
- `3_duplicated.ipynb`: Identifies duplicate tracks/rows and resolves them.
- `4_datetime.ipynb`: Parses dates, creates temporal indices, and builds time-based features.
- `5_loudness_normalization.ipynb`: Normalizes loudness and verifies consistency across time/genres.
- `6_EDA.ipynb`: Extended EDA using aggregated and split datasets (train/val/test); includes duration buckets, best-rank distribution, release timing (holiday/day-of-week), artist frequency analyses, audio feature outlier checks and boxplots, and correlation/scatter visuals.
- `7_1_Artist_Collabs_Calcu.ipynb`: Extracts artist appearances, computes artist statistics (song_count, avg_rank, best_rank), and builds lookup dictionaries.
- `7_2_Artist_Collabs_Calcu_test.ipynb`: Combines train+val for test-time lookups; saves pickles with artist stats, top collaborators, popularity, and global mean.
- `8_1_Feature_Engineering.ipynb`: Constructs ML feature tables from audio features, artist reputation metrics, and temporal features.
- `8_2_Feature_Engineering_test.ipynb`: Validates feature engineering on hold-out splits, checks schema and integrity before modeling.
- `9_Preprocessing_D_Reduction.ipynb`: Applies preprocessing steps and dimensionality reduction (e.g., PCA) as needed.
- `10_Model_Tuning_Test.ipynb`: Trains and tunes Random Forest, XGBoost, and NN baselines with temporal splits; outputs metrics and comparison plots.
- `11_cluster.ipynb`: (Suppliment Task) Performs clustering analyses to visualize and understand feature-space groupings.


## Notes on Temporal Splitting and Leakage Prevention
- We adopt temporal train/validation/test splits to mimic real-world forecasting and avoid using future data to predict the past.
- When engineering features, ensure any rolling or aggregations are computed using only past data relative to the prediction time.
- Do not shuffle across time; keep chronological order during split and evaluation.

## Questions / Troubleshooting
- If a notebook can’t find a CSV, verify the relative path and move the file to the indicated location.
- If package errors occur, verify Python 3.11 and the specified dependency ranges, then reinstall with `pip install -r requirements.txt`.
- For GPU-accelerated XGBoost, ensure CUDA and compatible binaries are installed (optional; CPU is sufficient for this coursework).

---

By following the environment setup, data placement, and run order above, another student or marker should be able to reproduce our reported results end-to-end.
