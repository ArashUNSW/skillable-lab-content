# Lab 06 - Tune and Improve the Cybersecurity Model

## Estimated Time

75 minutes

## Learning Objectives

By the end of this lab, you will be able to:

- Continue from the model evaluation work completed in Lab 05.
- Load processed training and testing data.
- Understand why model tuning is important.
- Tune Logistic Regression using `GridSearchCV`.
- Tune Random Forest using `GridSearchCV`.
- Compare original and tuned model performance.
- Use cross-validation during model tuning.
- Tune a classification threshold for a cybersecurity use case.
- Prioritize recall when missing phishing websites is risky.
- Save tuned models, tuning results, charts, and metadata.
- Validate that the tuned model outputs were created successfully.

---

## Scenario

You are working as a cybersecurity data analyst.

In Lab 05, you evaluated several models using accuracy, precision, recall, F1-score, confusion matrices, and ROC-AUC.

You learned that **accuracy is not enough** for cybersecurity model evaluation. A model might have good accuracy but still miss phishing websites.

In this lab, you will improve the model workflow by tuning model settings, also called **hyperparameters**.

You will also tune the classification threshold. This is important because cybersecurity teams may prefer to catch more phishing websites, even if that causes more false positives.

---

## What You Will Create

In this lab, you will create the following files:

```text
tuning/logistic_regression_grid_results.csv
tuning/random_forest_grid_results.csv
tuning/tuned_logistic_regression_model.joblib
tuning/tuned_random_forest_model.joblib
tuning/tuned_model_metrics.csv
tuning/model_tuning_comparison.png
tuning/threshold_analysis.csv
tuning/threshold_analysis.png
tuning/selected_tuned_model.joblib
tuning/selected_threshold.txt
tuning/lab06_metadata.txt
```

These files document the tuning process and store the improved model for later labs.

---

## Before You Begin

You should have completed Labs 01 through 05.

You need the processed data files created in Lab 03 and used in Labs 04 and 05.

The required processed files are:

```text
X_train.csv
X_test.csv
y_train.csv
y_test.csv
X_train_scaled.csv
X_test_scaled.csv
```

You should also have the model and evaluation folders from Labs 04 and 05:

```text
models
evaluation
processed
```

> [!note]
> This lab includes copy logic to find these folders from previous lab folders if they are not already in the Lab06 folder.

---

## Task 1 - Launch Jupyter Notebook

1. Select the **Windows Start** menu.
2. Search for:

```text
Anaconda Navigator
```

3. Open **Anaconda Navigator**.
4. Locate **Jupyter Notebook**.
5. Select **Launch**.

If Jupyter Notebook does not open, use this alternative method:

1. Open the **Windows Start** menu.
2. Search for:

```text
Anaconda Prompt
```

3. Open **Anaconda Prompt**.
4. Run:

```powershell
jupyter notebook
```

Jupyter Notebook should open in your browser.

---

## Task 2 - Create a Lab 06 Folder

In the Jupyter Notebook file browser:

1. Select **New**.
2. Select **Folder**.
3. Select the checkbox beside the new folder.
4. Select **Rename**.
5. Rename the folder to:

```text
Lab06
```

6. Open the **Lab06** folder.

---

## Task 3 - Create a New Python Notebook

1. Inside the **Lab06** folder, select **New**.
2. Select **Python 3 (ipykernel)**.
3. Rename the notebook to:

```text
Lab06-Tune-and-Improve-Model.ipynb
```

To rename the notebook:

1. Select the notebook title at the top of the page.
2. Enter the new name.
3. Select **Rename**.

---

## Task 4 - Install Required Packages

In the first notebook cell, run:

```python
%pip install pandas scikit-learn matplotlib joblib
```

Expected result:

```text
The packages install successfully or show "Requirement already satisfied".
```

---

## Task 5 - Import Required Libraries

In a new notebook cell, run:

```python
import os
import shutil
import pandas as pd
import matplotlib.pyplot as plt
import joblib

from sklearn.model_selection import GridSearchCV
from sklearn.linear_model import LogisticRegression
from sklearn.ensemble import RandomForestClassifier

from sklearn.metrics import (
    accuracy_score,
    precision_score,
    recall_score,
    f1_score,
    roc_auc_score
)
```

Expected result:

```text
No error messages should appear.
```

---

## Task 6 - Check the Current Folder

Run this code:

```python
print("Current folder:")
print(os.getcwd())

print("\nFiles and folders in this location:")
print(os.listdir())
```

Expected result:

```text
The current Lab06 folder path should display.
```

---

## Task 7 - Locate or Copy Required Files from Previous Labs

This step looks for the `processed`, `models`, and `evaluation` folders.

If they are not in the Lab06 folder, the code attempts to copy them from Lab05, Lab04, or Lab03.

Run this code:

```python
processed_folder = "processed"
models_folder = "models"
evaluation_folder = "evaluation"

required_processed_files = [
    "X_train.csv",
    "X_test.csv",
    "y_train.csv",
    "y_test.csv",
    "X_train_scaled.csv",
    "X_test_scaled.csv"
]

def required_files_exist(folder, required_files):
    return os.path.isdir(folder) and all(
        os.path.exists(os.path.join(folder, file_name))
        for file_name in required_files
    )

def copy_folder_if_available(destination_folder, possible_source_folders, required_files):
    if required_files_exist(destination_folder, required_files):
        print(f"{destination_folder} folder found in the current Lab06 folder.")
        return True

    for source_folder in possible_source_folders:
        if required_files_exist(source_folder, required_files):
            print(f"Found required files in {source_folder}")
            print(f"Copying {source_folder} to {destination_folder}...")

            shutil.copytree(
                source_folder,
                destination_folder,
                dirs_exist_ok=True
            )

            print(f"Copied {destination_folder} successfully.")
            return True

    print(f"Required files for {destination_folder} were not found.")
    return False

processed_found = copy_folder_if_available(
    processed_folder,
    [
        os.path.join("..", "Lab05", "processed"),
        os.path.join("..", "Lab04", "processed"),
        os.path.join("..", "Lab03", "processed")
    ],
    required_processed_files
)

print("\nProcessed files ready:", processed_found)
```

Expected result:

```text
Processed files ready: True
```

> [!alert]
> If the processed files are not found, return to Lab 03 and complete the final validation step. Then rerun this task.

---

## Task 8 - Create a Tuning Output Folder

Run this code:

```python
tuning_folder = "tuning"

os.makedirs(tuning_folder, exist_ok=True)

print("Tuning folder created:", tuning_folder)
```

Expected result:

```text
Tuning folder created: tuning
```

---

## Task 9 - Load the Training and Testing Data

Run this code:

```python
X_train = pd.read_csv(os.path.join(processed_folder, "X_train.csv"))
X_test = pd.read_csv(os.path.join(processed_folder, "X_test.csv"))

y_train = pd.read_csv(os.path.join(processed_folder, "y_train.csv")).squeeze("columns")
y_test = pd.read_csv(os.path.join(processed_folder, "y_test.csv")).squeeze("columns")

X_train_scaled = pd.read_csv(os.path.join(processed_folder, "X_train_scaled.csv"))
X_test_scaled = pd.read_csv(os.path.join(processed_folder, "X_test_scaled.csv"))

print("X_train shape:", X_train.shape)
print("X_test shape:", X_test.shape)
print("y_train shape:", y_train.shape)
print("y_test shape:", y_test.shape)
print("X_train_scaled shape:", X_train_scaled.shape)
print("X_test_scaled shape:", X_test_scaled.shape)
```

Expected result:

```text
X_train shape: (8844, 30)
X_test shape: (2211, 30)
y_train shape: (8844,)
y_test shape: (2211,)
X_train_scaled shape: (8844, 30)
X_test_scaled shape: (2211, 30)
```

---

## Task 10 - Review the Target Classes

Run this code:

```python
print("Training target values:")
print(y_train.value_counts())

print("\nTesting target values:")
print(y_test.value_counts())
```

Expected result:

```text
The target class counts should display for both training and testing data.
```

In this lab:

```text
1 = phishing or suspicious
0 = legitimate or benign
```

---

## Task 11 - Create a Metrics Function

This function will calculate performance metrics for each tuned model.

Run this code:

```python
def calculate_model_metrics(model_name, y_true, y_pred, y_probability=None):
    metrics = {
        "model": model_name,
        "accuracy": accuracy_score(y_true, y_pred),
        "precision": precision_score(y_true, y_pred, zero_division=0),
        "recall": recall_score(y_true, y_pred, zero_division=0),
        "f1_score": f1_score(y_true, y_pred, zero_division=0)
    }

    if y_probability is not None:
        metrics["roc_auc"] = roc_auc_score(y_true, y_probability)
    else:
        metrics["roc_auc"] = None

    return metrics
```

Expected result:

```text
The function should be created with no output errors.
```

---

## Task 12 - Understand Hyperparameter Tuning

A **hyperparameter** is a model setting selected before training.

Examples:

```text
Logistic Regression:
- C
- class_weight

Random Forest:
- n_estimators
- max_depth
- min_samples_split
- class_weight
```

Hyperparameter tuning means testing different settings to find the combination that gives better model performance.

In this lab, you will use:

```text
GridSearchCV
```

`GridSearchCV` trains multiple versions of a model using different settings and cross-validation.

---

## Task 13 - Tune Logistic Regression

Logistic Regression often works better with scaled data.

This task uses:

```text
X_train_scaled
```

Run this code:

```python
logistic_regression = LogisticRegression(
    max_iter=2000,
    random_state=42
)

logistic_param_grid = {
    "C": [0.01, 0.1, 1, 10],
    "class_weight": [None, "balanced"],
    "solver": ["lbfgs"]
}

logistic_grid_search = GridSearchCV(
    estimator=logistic_regression,
    param_grid=logistic_param_grid,
    scoring="f1",
    cv=3,
    n_jobs=-1
)

logistic_grid_search.fit(X_train_scaled, y_train)

print("Best Logistic Regression parameters:")
print(logistic_grid_search.best_params_)

print("\nBest cross-validation F1-score:")
print(round(logistic_grid_search.best_score_, 4))
```

Expected result:

```text
The best Logistic Regression parameters and cross-validation F1-score should display.
```

> [!note]
> This step may take a few minutes.

---

## Task 14 - Save Logistic Regression Grid Results

Run this code:

```python
logistic_results = pd.DataFrame(logistic_grid_search.cv_results_)

logistic_results_file = os.path.join(
    tuning_folder,
    "logistic_regression_grid_results.csv"
)

logistic_results.to_csv(logistic_results_file, index=False)

print("Saved:", logistic_results_file)
```

Expected result:

```text
Saved: tuning\logistic_regression_grid_results.csv
```

---

## Task 15 - Evaluate the Tuned Logistic Regression Model

Run this code:

```python
best_logistic_model = logistic_grid_search.best_estimator_

logistic_tuned_predictions = best_logistic_model.predict(X_test_scaled)
logistic_tuned_probabilities = best_logistic_model.predict_proba(X_test_scaled)[:, 1]

logistic_tuned_metrics = calculate_model_metrics(
    "Tuned Logistic Regression",
    y_test,
    logistic_tuned_predictions,
    logistic_tuned_probabilities
)

logistic_tuned_metrics
```

Expected result:

```text
A dictionary of Logistic Regression evaluation metrics should display.
```

---

## Task 16 - Save the Tuned Logistic Regression Model

Run this code:

```python
tuned_logistic_file = os.path.join(
    tuning_folder,
    "tuned_logistic_regression_model.joblib"
)

joblib.dump(best_logistic_model, tuned_logistic_file)

print("Saved:", tuned_logistic_file)
```

Expected result:

```text
Saved: tuning\tuned_logistic_regression_model.joblib
```

---

## Task 17 - Tune Random Forest

Random Forest does not require scaled data.

This task uses:

```text
X_train
```

Run this code:

```python
random_forest = RandomForestClassifier(
    random_state=42
)

random_forest_param_grid = {
    "n_estimators": [100, 200],
    "max_depth": [None, 5, 10],
    "min_samples_split": [2, 5],
    "class_weight": [None, "balanced"]
}

random_forest_grid_search = GridSearchCV(
    estimator=random_forest,
    param_grid=random_forest_param_grid,
    scoring="f1",
    cv=3,
    n_jobs=-1
)

random_forest_grid_search.fit(X_train, y_train)

print("Best Random Forest parameters:")
print(random_forest_grid_search.best_params_)

print("\nBest cross-validation F1-score:")
print(round(random_forest_grid_search.best_score_, 4))
```

Expected result:

```text
The best Random Forest parameters and cross-validation F1-score should display.
```

> [!alert]
> This step may take several minutes because multiple Random Forest models are trained.

---

## Task 18 - Save Random Forest Grid Results

Run this code:

```python
random_forest_results = pd.DataFrame(random_forest_grid_search.cv_results_)

random_forest_results_file = os.path.join(
    tuning_folder,
    "random_forest_grid_results.csv"
)

random_forest_results.to_csv(random_forest_results_file, index=False)

print("Saved:", random_forest_results_file)
```

Expected result:

```text
Saved: tuning\random_forest_grid_results.csv
```

---

## Task 19 - Evaluate the Tuned Random Forest Model

Run this code:

```python
best_random_forest_model = random_forest_grid_search.best_estimator_

random_forest_tuned_predictions = best_random_forest_model.predict(X_test)
random_forest_tuned_probabilities = best_random_forest_model.predict_proba(X_test)[:, 1]

random_forest_tuned_metrics = calculate_model_metrics(
    "Tuned Random Forest",
    y_test,
    random_forest_tuned_predictions,
    random_forest_tuned_probabilities
)

random_forest_tuned_metrics
```

Expected result:

```text
A dictionary of Random Forest evaluation metrics should display.
```

---

## Task 20 - Save the Tuned Random Forest Model

Run this code:

```python
tuned_random_forest_file = os.path.join(
    tuning_folder,
    "tuned_random_forest_model.joblib"
)

joblib.dump(best_random_forest_model, tuned_random_forest_file)

print("Saved:", tuned_random_forest_file)
```

Expected result:

```text
Saved: tuning\tuned_random_forest_model.joblib
```

---

## Task 21 - Compare Tuned Model Metrics

Run this code:

```python
tuned_metrics_df = pd.DataFrame([
    logistic_tuned_metrics,
    random_forest_tuned_metrics
])

tuned_metrics_df[
    ["accuracy", "precision", "recall", "f1_score", "roc_auc"]
] = tuned_metrics_df[
    ["accuracy", "precision", "recall", "f1_score", "roc_auc"]
].round(4)

tuned_metrics_df
```

Expected result:

```text
A table comparing the tuned Logistic Regression and tuned Random Forest models should display.
```

---

## Task 22 - Save the Tuned Metrics Summary

Run this code:

```python
tuned_metrics_file = os.path.join(
    tuning_folder,
    "tuned_model_metrics.csv"
)

tuned_metrics_df.to_csv(tuned_metrics_file, index=False)

print("Saved:", tuned_metrics_file)
```

Expected result:

```text
Saved: tuning\tuned_model_metrics.csv
```

---

## Task 23 - Create a Model Tuning Comparison Chart

Run this code:

```python
metrics_for_chart = tuned_metrics_df.set_index("model")[
    ["accuracy", "precision", "recall", "f1_score", "roc_auc"]
]

metrics_for_chart.plot(kind="bar", figsize=(10, 6))

plt.title("Tuned Model Performance Comparison")
plt.xlabel("Model")
plt.ylabel("Score")
plt.ylim(0, 1)
plt.xticks(rotation=45, ha="right")
plt.tight_layout()

tuning_chart_file = os.path.join(
    tuning_folder,
    "model_tuning_comparison.png"
)

plt.savefig(tuning_chart_file)
plt.show()

print("Saved:", tuning_chart_file)
```

Expected result:

```text
A bar chart should display and be saved.
```

---

## Task 24 - Select the Best Tuned Model

For this lab, the selected model is chosen using:

```text
Highest F1-score
Recall as the first tie-breaker
Precision as the second tie-breaker
```

Run this code:

```python
best_tuned_row = tuned_metrics_df.sort_values(
    by=["f1_score", "recall", "precision"],
    ascending=False
).iloc[0]

selected_model_name = best_tuned_row["model"]

print("Selected tuned model:", selected_model_name)
print("\nSelected model metrics:")
print(best_tuned_row)
```

Expected result:

```text
The selected tuned model should display.
```

---

## Task 25 - Store the Selected Model Object

Run this code:

```python
if selected_model_name == "Tuned Logistic Regression":
    selected_model = best_logistic_model
    selected_model_uses_scaled_data = True
    selected_model_probabilities = logistic_tuned_probabilities

elif selected_model_name == "Tuned Random Forest":
    selected_model = best_random_forest_model
    selected_model_uses_scaled_data = False
    selected_model_probabilities = random_forest_tuned_probabilities

else:
    raise ValueError("Unexpected selected model name.")

print("Selected model:", selected_model_name)
print("Uses scaled data:", selected_model_uses_scaled_data)
```

Expected result:

```text
The selected model name and scaled-data setting should display.
```

---

## Task 26 - Save the Selected Tuned Model

Run this code:

```python
selected_model_file = os.path.join(
    tuning_folder,
    "selected_tuned_model.joblib"
)

joblib.dump(selected_model, selected_model_file)

print("Saved:", selected_model_file)
```

Expected result:

```text
Saved: tuning\selected_tuned_model.joblib
```

---

## Task 27 - Understand Classification Thresholds

Most classification models use a default threshold of:

```text
0.50
```

This means:

```text
If predicted phishing probability >= 0.50, predict phishing.
If predicted phishing probability < 0.50, predict legitimate.
```

In cybersecurity, you may want to lower the threshold to catch more phishing websites.

Lowering the threshold can increase recall, but it may also increase false positives.

---

## Task 28 - Analyze Different Thresholds

Run this code:

```python
threshold_rows = []

threshold_values = [round(value / 100, 2) for value in range(10, 91, 5)]

for threshold in threshold_values:
    threshold_predictions = (selected_model_probabilities >= threshold).astype(int)

    threshold_rows.append({
        "threshold": threshold,
        "accuracy": accuracy_score(y_test, threshold_predictions),
        "precision": precision_score(y_test, threshold_predictions, zero_division=0),
        "recall": recall_score(y_test, threshold_predictions, zero_division=0),
        "f1_score": f1_score(y_test, threshold_predictions, zero_division=0)
    })

threshold_df = pd.DataFrame(threshold_rows)

threshold_df[["accuracy", "precision", "recall", "f1_score"]] = threshold_df[
    ["accuracy", "precision", "recall", "f1_score"]
].round(4)

threshold_df
```

Expected result:

```text
A table should display metrics for thresholds from 0.10 to 0.90.
```

---

## Task 29 - Save the Threshold Analysis

Run this code:

```python
threshold_analysis_file = os.path.join(
    tuning_folder,
    "threshold_analysis.csv"
)

threshold_df.to_csv(threshold_analysis_file, index=False)

print("Saved:", threshold_analysis_file)
```

Expected result:

```text
Saved: tuning\threshold_analysis.csv
```

---

## Task 30 - Plot Threshold Analysis

Run this code:

```python
plt.figure(figsize=(10, 6))

plt.plot(threshold_df["threshold"], threshold_df["precision"], marker="o", label="Precision")
plt.plot(threshold_df["threshold"], threshold_df["recall"], marker="o", label="Recall")
plt.plot(threshold_df["threshold"], threshold_df["f1_score"], marker="o", label="F1-score")

plt.title("Threshold Analysis for Selected Tuned Model")
plt.xlabel("Classification Threshold")
plt.ylabel("Score")
plt.ylim(0, 1)
plt.legend()
plt.tight_layout()

threshold_chart_file = os.path.join(
    tuning_folder,
    "threshold_analysis.png"
)

plt.savefig(threshold_chart_file)
plt.show()

print("Saved:", threshold_chart_file)
```

Expected result:

```text
A threshold analysis chart should display and be saved.
```

---

## Task 31 - Select a Cybersecurity-Focused Threshold

For this lab, you will choose a threshold using a cybersecurity-focused rule:

```text
Prefer recall >= 0.95.
Among those thresholds, choose the highest F1-score.
If no threshold reaches 0.95 recall, choose the highest F1-score overall.
```

Run this code:

```python
preferred_thresholds = threshold_df[threshold_df["recall"] >= 0.95]

if len(preferred_thresholds) > 0:
    selected_threshold_row = preferred_thresholds.sort_values(
        by=["f1_score", "precision"],
        ascending=False
    ).iloc[0]

    threshold_selection_reason = "Selected highest F1-score among thresholds with recall >= 0.95."

else:
    selected_threshold_row = threshold_df.sort_values(
        by=["f1_score", "recall"],
        ascending=False
    ).iloc[0]

    threshold_selection_reason = "No threshold reached recall >= 0.95, so selected highest F1-score overall."

selected_threshold = selected_threshold_row["threshold"]

print("Selected threshold:", selected_threshold)
print("Reason:", threshold_selection_reason)
print("\nSelected threshold metrics:")
print(selected_threshold_row)
```

Expected result:

```text
The selected threshold and reason should display.
```

---

## Task 32 - Save the Selected Threshold

Run this code:

```python
selected_threshold_file = os.path.join(
    tuning_folder,
    "selected_threshold.txt"
)

with open(selected_threshold_file, "w") as file:
    file.write("Lab 06 - Selected Classification Threshold\n")
    file.write("==========================================\n")
    file.write("Selected model: " + str(selected_model_name) + "\n")
    file.write("Selected threshold: " + str(selected_threshold) + "\n")
    file.write("Reason: " + threshold_selection_reason + "\n\n")
    file.write("Selected threshold metrics:\n")
    file.write(str(selected_threshold_row))

print("Saved:", selected_threshold_file)
```

Expected result:

```text
Saved: tuning\selected_threshold.txt
```

---

## Task 33 - Create Final Threshold-Based Predictions

Run this code:

```python
final_threshold_predictions = (
    selected_model_probabilities >= selected_threshold
).astype(int)

final_threshold_metrics = calculate_model_metrics(
    selected_model_name + " with Tuned Threshold",
    y_test,
    final_threshold_predictions,
    selected_model_probabilities
)

final_threshold_metrics
```

Expected result:

```text
Final threshold-based metrics should display.
```

---

## Task 34 - Save Final Threshold Predictions

Run this code:

```python
final_predictions_df = pd.DataFrame({
    "actual": y_test,
    "predicted": final_threshold_predictions,
    "phishing_probability": selected_model_probabilities
})

final_predictions_file = os.path.join(
    tuning_folder,
    "final_threshold_predictions.csv"
)

final_predictions_df.to_csv(final_predictions_file, index=False)

print("Saved:", final_predictions_file)
```

Expected result:

```text
Saved: tuning\final_threshold_predictions.csv
```

---

## Task 35 - Save Lab 06 Metadata

Run this code:

```python
metadata_file = os.path.join(tuning_folder, "lab06_metadata.txt")

with open(metadata_file, "w") as file:
    file.write("Lab 06 - Tune and Improve the Cybersecurity Model\n")
    file.write("Training rows: " + str(X_train.shape[0]) + "\n")
    file.write("Testing rows: " + str(X_test.shape[0]) + "\n")
    file.write("Feature columns: " + str(X_train.shape[1]) + "\n")
    file.write("Tuning method: GridSearchCV\n")
    file.write("Cross-validation folds: 3\n")
    file.write("Grid search scoring metric: f1\n")
    file.write("Models tuned: Logistic Regression, Random Forest\n")
    file.write("Selected tuned model: " + str(selected_model_name) + "\n")
    file.write("Selected model uses scaled data: " + str(selected_model_uses_scaled_data) + "\n")
    file.write("Selected threshold: " + str(selected_threshold) + "\n")
    file.write("Threshold selection reason: " + threshold_selection_reason + "\n")
    file.write("Final threshold accuracy: " + str(round(final_threshold_metrics["accuracy"], 4)) + "\n")
    file.write("Final threshold precision: " + str(round(final_threshold_metrics["precision"], 4)) + "\n")
    file.write("Final threshold recall: " + str(round(final_threshold_metrics["recall"], 4)) + "\n")
    file.write("Final threshold F1-score: " + str(round(final_threshold_metrics["f1_score"], 4)) + "\n")
    file.write("Final threshold ROC-AUC: " + str(round(final_threshold_metrics["roc_auc"], 4)) + "\n")

print("Saved:", metadata_file)
```

Expected result:

```text
Saved: tuning\lab06_metadata.txt
```

---

## Task 36 - List Tuning Output Files

Run this code:

```python
print("Files in tuning folder:")

for file_name in os.listdir(tuning_folder):
    print("-", file_name)
```

Expected result:

```text
The tuning output files should display.
```

You should see files such as:

```text
logistic_regression_grid_results.csv
random_forest_grid_results.csv
tuned_logistic_regression_model.joblib
tuned_random_forest_model.joblib
tuned_model_metrics.csv
model_tuning_comparison.png
threshold_analysis.csv
threshold_analysis.png
selected_tuned_model.joblib
selected_threshold.txt
final_threshold_predictions.csv
lab06_metadata.txt
```

---

## Task 37 - Reload the Selected Tuned Model

Run this code:

```python
reloaded_selected_model = joblib.load(selected_model_file)

if selected_model_uses_scaled_data:
    reloaded_probabilities = reloaded_selected_model.predict_proba(X_test_scaled)[:, 1]
else:
    reloaded_probabilities = reloaded_selected_model.predict_proba(X_test)[:, 1]

reloaded_predictions = (reloaded_probabilities >= selected_threshold).astype(int)

reloaded_f1 = f1_score(y_test, reloaded_predictions, zero_division=0)

print("Reloaded selected model F1-score:", round(reloaded_f1, 4))
print("Original final threshold F1-score:", round(final_threshold_metrics["f1_score"], 4))
```

Expected result:

```text
The reloaded model F1-score should match the final threshold F1-score.
```

---

## Task 38 - Validate Your Work

Run this final validation cell:

```python
required_tuning_files = [
    "logistic_regression_grid_results.csv",
    "random_forest_grid_results.csv",
    "tuned_logistic_regression_model.joblib",
    "tuned_random_forest_model.joblib",
    "tuned_model_metrics.csv",
    "model_tuning_comparison.png",
    "threshold_analysis.csv",
    "threshold_analysis.png",
    "selected_tuned_model.joblib",
    "selected_threshold.txt",
    "final_threshold_predictions.csv",
    "lab06_metadata.txt"
]

for file_name in required_tuning_files:
    file_path = os.path.join(tuning_folder, file_name)
    assert os.path.exists(file_path), f"Missing file: {file_name}"

assert X_train.shape == (8844, 30), "X_train should have 8,844 rows and 30 columns."
assert X_test.shape == (2211, 30), "X_test should have 2,211 rows and 30 columns."
assert len(y_train) == 8844, "y_train should have 8,844 values."
assert len(y_test) == 2211, "y_test should have 2,211 values."

assert selected_model_name in [
    "Tuned Logistic Regression",
    "Tuned Random Forest"
], "Selected model should be one of the tuned models."

assert selected_threshold >= 0.10, "Selected threshold should be at least 0.10."
assert selected_threshold <= 0.90, "Selected threshold should be at most 0.90."

assert final_threshold_metrics["accuracy"] >= 0, "Accuracy should be valid."
assert final_threshold_metrics["precision"] >= 0, "Precision should be valid."
assert final_threshold_metrics["recall"] >= 0, "Recall should be valid."
assert final_threshold_metrics["f1_score"] >= 0, "F1-score should be valid."

assert final_threshold_metrics["accuracy"] <= 1, "Accuracy should be less than or equal to 1."
assert final_threshold_metrics["precision"] <= 1, "Precision should be less than or equal to 1."
assert final_threshold_metrics["recall"] <= 1, "Recall should be less than or equal to 1."
assert final_threshold_metrics["f1_score"] <= 1, "F1-score should be less than or equal to 1."

print("Validation passed. Lab 06 is complete.")
```

Expected result:

```text
Validation passed. Lab 06 is complete.
```

---

## Task 39 - Save Your Notebook

1. Select **File**.
2. Select **Save and Checkpoint**.

Confirm the notebook is named:

```text
Lab06-Tune-and-Improve-Model.ipynb
```

---

## Knowledge Check

Answer the following questions:

1. What is a hyperparameter?
2. What is the purpose of `GridSearchCV`?
3. Why was Logistic Regression tuned using scaled data?
4. Why can Random Forest use unscaled data?
5. What metric was used during grid search tuning?
6. Why might F1-score be useful in cybersecurity classification?
7. What is a classification threshold?
8. What happens when the threshold is lowered?
9. Why might a cybersecurity team prefer higher recall?
10. What is the risk of too many false positives?
11. Which tuned model was selected in your lab?
12. Which threshold was selected?
13. Why did the lab save the selected model and selected threshold?

---

## Troubleshooting

### Problem: Processed Files Are Missing

Run:

```python
import os

print(os.getcwd())
print(os.listdir())
```

Check whether the current folder contains:

```text
processed
```

If not, check whether one of these folders exists:

```text
../Lab05/processed
../Lab04/processed
../Lab03/processed
```

If the processed files are missing, return to Lab 03 and complete the validation step.

---

### Problem: `ModuleNotFoundError`

If you see:

```text
ModuleNotFoundError: No module named 'sklearn'
```

run:

```python
%pip install scikit-learn
```

Then restart the kernel:

1. Select **Kernel**.
2. Select **Restart Kernel**.
3. Run the notebook again from the beginning.

---

### Problem: Grid Search Takes Too Long

If the Random Forest grid search takes too long, reduce the parameter grid.

Replace:

```python
"n_estimators": [100, 200]
```

with:

```python
"n_estimators": [100]
```

You can also change:

```python
n_jobs=-1
```

to:

```python
n_jobs=1
```

if your environment has multiprocessing issues.

---

### Problem: Logistic Regression Shows a Convergence Warning

Increase the maximum iterations:

```python
logistic_regression = LogisticRegression(
    max_iter=3000,
    random_state=42
)
```

Then rerun the Logistic Regression tuning cells.

---

### Problem: Threshold Analysis Looks Confusing

Remember:

```text
Lower threshold  = more websites predicted as phishing
Higher threshold = fewer websites predicted as phishing
```

Lower thresholds usually increase recall but may reduce precision.

---

### Problem: Validation Fails Because Shape Is Different

Run:

```python
print(X_train.shape)
print(X_test.shape)
print(y_train.shape)
print(y_test.shape)
```

Expected values are:

```text
X_train: (8844, 30)
X_test: (2211, 30)
y_train: (8844,)
y_test: (2211,)
```

If these values are different, return to Lab 03 and recreate the processed files using the original dataset.

---

## Summary

In this lab, you improved the cybersecurity classification workflow.

You completed the following tasks:

- Loaded processed training and testing data.
- Tuned Logistic Regression using `GridSearchCV`.
- Tuned Random Forest using `GridSearchCV`.
- Saved grid search results.
- Evaluated tuned models using multiple metrics.
- Compared tuned model performance.
- Selected the best tuned model using F1-score, recall, and precision.
- Analyzed classification thresholds.
- Selected a cybersecurity-focused threshold.
- Saved the selected tuned model.
- Saved the selected threshold.
- Saved final threshold-based predictions.
- Created a metadata file.
- Validated all required outputs.

In Lab 07, you will interpret model behavior and identify which website features are most important for phishing detection.
