# Hybrid ConvLSTM Transfer Learning for Arecanut Fruit-Rot Risk Prediction



## 1\. Repository contents

* `main\\\_transfer\\\_learning.py` — complete preprocessing, spatial-grid construction,
sequence generation, model definitions, source-domain training, Kerala transfer learning,
baseline comparisons, evaluation, ROC analysis, and spatial sensitivity analysis.
* `requirements.txt` — Python package dependencies used by the implementation.
* `data/README.md` — instructions for supplying the input datasets.
* `results/README.md` — description of generated result files.
* `figures/README.md` — description of generated figures.

## 2\. Input datasets

The executable expects:

* `data/combined\\\_08\\\_2026.xlsx` — Karnataka source-domain dataset
* `data/kerala\\\_19\\\_2026.xlsx` — Kerala target-domain dataset

## 3\. Main experimental configuration

The final implementation uses:

* Source domain: Karnataka
* Target domain: Kerala
* Karnataka source split: 80% chronological training / 20% chronological validation
* Kerala split: 70% chronological adaptation / 30% chronological unseen test
* Kerala adaptation split: 90% chronological fine-tuning / 10% chronological validation
* Temporal sequence length: 8 consecutive daily observations
* Weekly lag features: lags 1, 2, and 3
* Karnataka grid: 4 × 5
* Kerala grid: 2 × 5
* Disease classes: Low, Moderate, Severe
* Model seeds: 42, 52, 62
* Source epochs: 50
* Transfer-learning epochs: 40
* Source learning rate: 5 × 10^-4
* Transfer learning rate: 1 × 10^-4
* Batch size: 32
* Dropout: 0.15
* Early stopping patience: 10
* Learning-rate reduction patience: 5

## 4\. Reproducibility procedure

Run:

```bash
pip install -r requirements.txt
python main\\\_transfer\\\_learning.py
```

The script:

1. Loads and validates the Karnataka and Kerala datasets.
2. Creates the disease-risk classes using the thresholds implemented in the code.
3. Performs the required preprocessing and weekly lag-feature generation.
4. Builds coordinate-based spatial grids.
5. Performs chronological source and target-domain splitting.
6. Fits preprocessing statistics on training/adaptation data rather than the final unseen test labels.
7. Creates target-date-based 8-day sequences.
8. Trains the Karnataka Hybrid ConvLSTM source model.
9. Performs Kerala transfer learning.
10. Trains the Kerala baseline models.
11. Evaluates all models on the same final unseen Kerala test targets.
12. Repeats the experiments for seeds 42, 52, and 62.
13. Performs the randomized spatial-grid sensitivity analysis using mapping seeds 101, 202, and 303.
14. Saves machine-readable metrics and figures.

## 5\. Sequence-generation rule

Sequences are generated from the complete chronological timeline and assigned according to
their target date. Therefore, when a target belongs to the beginning of the unseen Kerala test
period, its preceding historical observations can be used as input because those observations
occur before the prediction target. The target label itself is not used for training.

The implementation also requires strict daily continuity within each sequence.

## 6\. Evaluation metrics

The implementation reports:

* Accuracy
* Balanced Accuracy
* Macro Precision
* Macro Recall
* Macro F1
* Weighted F1
* Multiclass ROC-AUC

Per-seed results and mean ± standard deviation summaries are saved as CSV files.

## 7\. Baseline models

The final comparison includes:

* Logistic Regression
* Random Forest
* XGBoost
* LSTM
* CNN-LSTM
* Hybrid ConvLSTM trained from scratch
* Karnataka-to-Kerala Transfer Hybrid ConvLSTM

## 8\. Spatial sensitivity analysis

The final script additionally compares the geographic coordinate-based Kerala grid with
randomized location-to-grid-cell assignments. Randomized mapping seeds are:

* 101
* 202
* 303

Only the location-to-grid-cell assignment is randomized; the data, labels, dates, model
settings, and model seeds remain unchanged.

## 9\. Generated results

The main output directory is created automatically by the script. Important files include:

* `Kerala\\\_Baseline\\\_Results\\\_All\\\_Seeds.csv`
* `Kerala\\\_Baseline\\\_Results\\\_Mean\\\_STD.csv`
* `Karnataka\\\_Source\\\_All\\\_Seeds.csv`
* `Karnataka\\\_Source\\\_Mean\\\_STD.csv`
* `Transfer\\\_Learning\\\_All\\\_Seeds.csv`
* `FINAL\\\_Model\\\_Comparison\\\_All\\\_Seeds.csv`
* `FINAL\\\_Model\\\_Comparison\\\_Mean\\\_STD.csv`
* `Spatial\\\_Sensitivity\\\_All\\\_Runs.csv`
* `Spatial\\\_Sensitivity\\\_Mean\\\_STD.csv`
* final Kerala test predictions
* classification reports
* confusion matrices
* ROC curves
* learning curves
* experiment configuration
* geographic and randomized grid mappings

## 10\. Data leakage safeguards

The final implementation explicitly separates target dates chronologically and does not use
final unseen Kerala test labels for model training. Preprocessing statistics are fitted using
the relevant training/adaptation data. Final test predictions are evaluated only after the
transfer-learning stage.

