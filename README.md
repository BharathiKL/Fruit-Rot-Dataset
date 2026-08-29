Hybrid ConvLSTM Transfer Learning for Arecanut Fruit-Rot Risk Prediction
This repository contains the implementation and reproducibility materials for the revised
Karnataka-to-Kerala Hybrid ConvLSTM transfer-learning experiments reported in the manuscript.
1. Repository contents
`main_transfer_learning.py` — complete preprocessing, spatial-grid construction,
sequence generation, model definitions, source-domain training, Kerala transfer learning,
baseline comparisons, evaluation, ROC analysis, and spatial sensitivity analysis.
`requirements.txt` — Python package dependencies used by the implementation.
`data/README.md` — instructions for supplying the input datasets.
`results/README.md` — description of generated result files.
`figures/README.md` — description of generated figures.
2. Input datasets
The executable expects:
`data/combined_08_2026.xlsx` — Karnataka source-domain dataset
`data/kerala_19_2026.xlsx` — Kerala target-domain dataset

3. Main experimental configuration
The final implementation uses:
Source domain: Karnataka
Target domain: Kerala
Karnataka source split: 80% chronological training / 20% chronological validation
Kerala split: 70% chronological adaptation / 30% chronological unseen test
Kerala adaptation split: 90% chronological fine-tuning / 10% chronological validation
Temporal sequence length: 8 consecutive daily observations
Weekly lag features: lags 1, 2, and 3
Karnataka grid: 4 × 5
Kerala grid: 2 × 5
Disease classes: Low, Moderate, Severe
Model seeds: 42, 52, 62
Source epochs: 50
Transfer-learning epochs: 40
Source learning rate: 5 × 10^-4
Transfer learning rate: 1 × 10^-4
Batch size: 32
Dropout: 0.15
Early stopping patience: 10
Learning-rate reduction patience: 5
4. Reproducibility procedure
Run:
```bash
pip install -r requirements.txt
python main_transfer_learning.py
```
The script:
Loads and validates the Karnataka and Kerala datasets.
Creates the disease-risk classes using the thresholds implemented in the code.
Performs the required preprocessing and weekly lag-feature generation.
Builds coordinate-based spatial grids.
Performs chronological source and target-domain splitting.
Fits preprocessing statistics on training/adaptation data rather than the final unseen test labels.
Creates target-date-based 8-day sequences.
Trains the Karnataka Hybrid ConvLSTM source model.
Performs Kerala transfer learning.
Trains the Kerala baseline models.
Evaluates all models on the same final unseen Kerala test targets.
Repeats the experiments for seeds 42, 52, and 62.
Performs the randomized spatial-grid sensitivity analysis using mapping seeds 101, 202, and 303.
Saves machine-readable metrics and figures.
5. Sequence-generation rule
Sequences are generated from the complete chronological timeline and assigned according to
their target date. Therefore, when a target belongs to the beginning of the unseen Kerala test
period, its preceding historical observations can be used as input because those observations
occur before the prediction target. The target label itself is not used for training.
The implementation also requires strict daily continuity within each sequence.
6. Evaluation metrics
The implementation reports:
Accuracy
Balanced Accuracy
Macro Precision
Macro Recall
Macro F1
Weighted F1
Multiclass ROC-AUC
Per-seed results and mean ± standard deviation summaries are saved as CSV files.
7. Baseline models
The final comparison includes:
Logistic Regression
Random Forest
XGBoost
LSTM
CNN-LSTM
Hybrid ConvLSTM trained from scratch
Karnataka-to-Kerala Transfer Hybrid ConvLSTM
8. Spatial sensitivity analysis
The final script additionally compares the geographic coordinate-based Kerala grid with
randomized location-to-grid-cell assignments. Randomized mapping seeds are:
101
202
303
Only the location-to-grid-cell assignment is randomized; the data, labels, dates, model
settings, and model seeds remain unchanged.
9. Generated results
The main output directory is created automatically by the script. Important files include:
`Kerala_Baseline_Results_All_Seeds.csv`
`Kerala_Baseline_Results_Mean_STD.csv`
`Karnataka_Source_All_Seeds.csv`
`Karnataka_Source_Mean_STD.csv`
`Transfer_Learning_All_Seeds.csv`
`FINAL_Model_Comparison_All_Seeds.csv`
`FINAL_Model_Comparison_Mean_STD.csv`
`Spatial_Sensitivity_All_Runs.csv`
`Spatial_Sensitivity_Mean_STD.csv`
final Kerala test predictions
classification reports
confusion matrices
ROC curves
learning curves
experiment configuration
geographic and randomized grid mappings
10. Data leakage safeguards
The final implementation explicitly separates target dates chronologically and does not use
final unseen Kerala test labels for model training. Preprocessing statistics are fitted using
the relevant training/adaptation data. Final test predictions are evaluated only after the
transfer-learning stage.
