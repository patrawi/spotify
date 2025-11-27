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
The notebooks below reflect the actual filenames in this repository (the previous README list had off‑by‑one numbering and a missing/nonexistent `1_eda.ipynb`). Run them in order for a reproducible pipeline:

1. `1_outlier_loudness.ipynb` — Detect and correct loudness outliers; adjust extreme values.
2. `2_duplicated.ipynb` — Identify and remove duplicate rows (rank/title/date) while preserving distinct releases.
3. `3_datetime.ipynb` — Convert date strings to `datetime` and persist cleaned timestamps.
4. `4_loudness_normalization.ipynb` — Min‑max normalize loudness (after outlier handling) and save cleaned data.
5. `5_split_data.ipynb` — Temporal train/val/test split; aggregate per‑song performance; assign popularity labels; merge audio features.
6. `6_EDA.ipynb` — Extended EDA on aggregated & split datasets (distributions, timing, artist frequency, correlations, audio feature checks).
7. `7_1_Artist_Collabs_Calcu.ipynb` — Compute artist statistics (song counts, avg/best rank) & collaborator features from training split.
8. `7_2_Artist_Collabs_Calcu_test.ipynb` — Build train+val lookup artifacts for test evaluation; persist pickles.
9. `8_1_Feature_Engineering.ipynb` — Assemble ML feature tables (audio + reputation + temporal) and merge lookups.
10. `8_2_Feature_Engineering_test.ipynb` — Validate engineered features on hold‑out splits; ensure schema consistency.
11. `9_Preprocessing_D_Reduction.ipynb` — Perform preprocessing (scaling/encoding) and dimensionality reduction (e.g., PCA).
12. `10_Model_Tuning_Test.ipynb` — Train/tune models (RF, XGBoost, NN) using temporal splits; compare metrics.
13. `11_cluster.ipynb` — Clustering analyses to explore feature‑space structure and support EDA conclusions.

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
├── 1_outlier_loudness.ipynb
├── 2_duplicated.ipynb
├── 3_datetime.ipynb
├── 4_loudness_normalization.ipynb
├── 5_split_data.ipynb
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
- `1_outlier_loudness.ipynb`: Detects and corrects loudness outliers; adjusts extreme >0 dB values; prepares cleaned loudness data.
- `2_duplicated.ipynb`: Resolves duplicate rows (rank/title/date) and retains distinct release contexts; outputs de-duplicated dataset.
- `3_datetime.ipynb`: Converts date strings to `datetime`, ensuring consistent temporal typing for downstream splitting.
- `4_loudness_normalization.ipynb`: Applies MinMax scaling to loudness after outlier handling; persists normalized column.
- `5_split_data.ipynb`: Performs temporal train/val/test split; aggregates per-song performance metrics; computes popularity labels; merges unique audio features; saves aggregated datasets.
- `6_EDA.ipynb`: Extended EDA on aggregated datasets (timing patterns, artist frequencies, feature distributions, correlations, audio feature sanity checks).
- `7_1_Artist_Collabs_Calcu.ipynb`: Builds artist-level statistics (counts, avg/best rank) and collaborator-derived features from training split.
- `7_2_Artist_Collabs_Calcu_test.ipynb`: Generates combined train+val lookup artifacts (artist stats, collaborator info, popularity refs) for test evaluation.
- `8_1_Feature_Engineering.ipynb`: Assembles ML-ready feature tables (audio + reputation + temporal) and unifies lookup merges.
- `8_2_Feature_Engineering_test.ipynb`: Validates engineered feature schemas across splits; checks integrity before preprocessing/modeling.
- `9_Preprocessing_D_Reduction.ipynb`: Handles scaling/encoding and applies dimensionality reduction (e.g., PCA) where beneficial.
- `10_Model_Tuning_Test.ipynb`: Trains/tunes Random Forest, XGBoost, and NN models with temporal splits; compares performance metrics.
- `11_cluster.ipynb`: Performs clustering to explore feature-space structure and complement EDA findings.


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

