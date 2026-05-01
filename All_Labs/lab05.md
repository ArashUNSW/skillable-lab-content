# Lab 05 - Evaluate Cybersecurity Classification Models

## Estimated Time

60 minutes

## Learning Objectives

By the end of this lab, you will be able to:

- Continue from the trained models created in Lab 04.
- Load trained machine learning models from `.joblib` files.
- Load test data and prediction files.
- Evaluate model performance using accuracy, precision, recall, and F1-score.
- Create and interpret confusion matrices.
- Understand false positives and false negatives in a cybersecurity context.
- Compare multiple models using evaluation metrics.
- Create ROC curves and calculate ROC-AUC scores.
- Select a recommended model based on cybersecurity priorities.
- Save evaluation reports and charts for later labs.

---

## Scenario

You are working as a cybersecurity data analyst.

In Lab 04, you trained several machine learning models to classify websites as phishing or legitimate.

Training a model is only part of the workflow. You now need to evaluate the models carefully.

In cybersecurity, a model with high accuracy may still be risky if it misses phishing websites. Missing a phishing website is called a **false negative**, and it may allow a dangerous website to be treated as safe.

In this lab, you will evaluate the models using multiple metrics and decide which model is most suitable for a cybersecurity use case.

---

## What You Will Create

In this lab, you will create the following output files:

```text
evaluation/lab05_metrics_summary.csv
evaluation/lab05_recommended_model.txt
evaluation/baseline_classification_report.txt
evaluation/logistic_regression_classification_report.txt
evaluation/decision_tree_classification_report.txt
evaluation/random_forest_classification_report.txt
evaluation/logistic_regression_confusion_matrix.png
evaluation/decision_tree_confusion_matrix.png
evaluation/random_forest_confusion_matrix.png
evaluation/model_metrics_comparison.png
evaluation/roc_curve_comparison.png
evaluation/lab05_metadata.txt
```

These files will help document the model evaluation results.

---

## Before You Begin

You should have completed Lab 04.

Lab 04 created a folder named:

```text
models
```

The `models` folder should contain files such as:

```text
baseline_model.joblib
logistic_regression_model.joblib
decision_tree_model.joblib
random_forest_model.joblib
lab04_model_summary.csv
```

Lab 04 also used a folder named:

```text
processed
```

The `processed` folder should contain:

```text
X_test.csv
y_test.csv
X_test_scaled.csv
```

> [!note]
> This lab includes copy logic to find files from the Lab04 folder if they are not already in the current Lab05 folder.

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

## Task 2 - Create a Lab 05 Folder

In the Jupyter Notebook file browser:

1. Select **New**.
2. Select **Folder**.
3. Select the checkbox beside the new folder.
4. Select **Rename**.
5. Rename the folder to:

```text
Lab05
```

6. Open the **Lab05** folder.

---

## Task 3 - Create a New Python Notebook

1. Inside the **Lab05** folder, select **New**.
2. Select **Python 3 (ipykernel)**.
3. Rename the notebook to:

```text
Lab05-Evaluate-Classification-Models.ipynb
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

from sklearn.metrics import (
    accuracy_score,
    precision_score,
    recall_score,
    f1_score,
    confusion_matrix,
    classification_report,
    ConfusionMatrixDisplay,
    roc_curve,
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
The current Lab05 folder path should display.
```

---

## Task 7 - Locate or Copy Required Lab 04 Files

This step looks for the `models` and `processed` folders.

If they are not in the Lab05 folder, the code attempts to copy them from the Lab04 folder.

Run this code:

```python
models_folder = "models"
processed_folder = "processed"

required_model_files = [
    "baseline_model.joblib",
    "logistic_regression_model.joblib",
    "decision_tree_model.joblib",
    "random_forest_model.joblib"
]

required_processed_files = [
    "X_test.csv",
    "y_test.csv",
    "X_test_scaled.csv"
]

def required_files_exist(folder, required_files):
    return os.path.isdir(folder) and all(
        os.path.exists(os.path.join(folder, file_name))
        for file_name in required_files
    )

if not required_files_exist(models_folder, required_model_files):
    possible_lab04_models_folder = os.path.join("..", "Lab04", "models")

    if required_files_exist(possible_lab04_models_folder, required_model_files):
        print("Found models in ../Lab04/models")
        print("Copying models into the Lab05 folder...")

        shutil.copytree(
            possible_lab04_models_folder,
            models_folder,
            dirs_exist_ok=True
        )

        print("Models copied successfully.")
    else:
        print("Required model files were not found.")
        print("Complete Lab 04 before continuing.")

else:
    print("Models folder found in the current Lab05 folder.")

if not required_files_exist(processed_folder, required_processed_files):
    possible_lab04_processed_folder = os.path.join("..", "Lab04", "processed")
    possible_lab03_processed_folder = os.path.join("..", "Lab03", "processed")

    if required_files_exist(possible_lab04_processed_folder, required_processed_files):
        print("Found processed files in ../Lab04/processed")
        print("Copying processed files into the Lab05 folder...")

        shutil.copytree(
            possible_lab04_processed_folder,
            processed_folder,
            dirs_exist_ok=True
        )

        print("Processed files copied successfully.")

    elif required_files_exist(possible_lab03_processed_folder, required_processed_files):
        print("Found processed files in ../Lab03/processed")
        print("Copying processed files into the Lab05 folder...")

        shutil.copytree(
            possible_lab03_processed_folder,
            processed_folder,
            dirs_exist_ok=True
        )

        print("Processed files copied successfully.")

    else:
        print("Required processed files were not found.")
        print("Complete Lab 03 and Lab 04 before continuing.")

else:
    print("Processed folder found in the current Lab05 folder.")
```

Expected result:

```text
Models copied successfully.
Processed files copied successfully.
```

or:

```text
Models folder found in the current Lab05 folder.
Processed folder found in the current Lab05 folder.
```

---

## Task 8 - Verify Required Files

Run this code:

```python
print("Model files:")
for file_name in os.listdir(models_folder):
    print("-", file_name)

print("\nProcessed files:")
for file_name in os.listdir(processed_folder):
    print("-", file_name)
```

Expected result:

```text
The required model and processed files should display.
```

---

## Task 9 - Load the Test Data

Run this code:

```python
X_test = pd.read_csv(os.path.join(processed_folder, "X_test.csv"))
X_test_scaled = pd.read_csv(os.path.join(processed_folder, "X_test_scaled.csv"))
y_test = pd.read_csv(os.path.join(processed_folder, "y_test.csv")).squeeze("columns")

print("X_test shape:", X_test.shape)
print("X_test_scaled shape:", X_test_scaled.shape)
print("y_test shape:", y_test.shape)
```

Expected result:

```text
X_test shape: (2211, 30)
X_test_scaled shape: (2211, 30)
y_test shape: (2211,)
```

---

## Task 10 - Review the Test Target Distribution

Run this code:

```python
print("Test target class counts:")
print(y_test.value_counts())

print("\nTest target class percentages:")
print((y_test.value_counts(normalize=True) * 100).round(2))
```

Expected result:

```text
The test target class counts and percentages should display.
```

In this lab:

```text
1 = phishing or suspicious
0 = legitimate or benign
```

---

## Task 11 - Load the Trained Models

Run this code:

```python
baseline_model = joblib.load(os.path.join(models_folder, "baseline_model.joblib"))
logistic_regression_model = joblib.load(os.path.join(models_folder, "logistic_regression_model.joblib"))
decision_tree_model = joblib.load(os.path.join(models_folder, "decision_tree_model.joblib"))
random_forest_model = joblib.load(os.path.join(models_folder, "random_forest_model.joblib"))

print("All models loaded successfully.")
```

Expected result:

```text
All models loaded successfully.
```

---

## Task 12 - Generate Model Predictions

Logistic Regression was trained on scaled data.

Decision Tree and Random Forest were trained on unscaled data.

Run this code:

```python
baseline_predictions = baseline_model.predict(X_test)
logistic_regression_predictions = logistic_regression_model.predict(X_test_scaled)
decision_tree_predictions = decision_tree_model.predict(X_test)
random_forest_predictions = random_forest_model.predict(X_test)

print("Predictions generated successfully.")
```

Expected result:

```text
Predictions generated successfully.
```

---

## Task 13 - Create an Evaluation Folder

Run this code:

```python
evaluation_folder = "evaluation"

os.makedirs(evaluation_folder, exist_ok=True)

print("Evaluation folder created:", evaluation_folder)
```

Expected result:

```text
Evaluation folder created: evaluation
```

---

## Task 14 - Calculate Evaluation Metrics

Run this code:

```python
def calculate_metrics(model_name, y_true, y_pred):
    return {
        "model": model_name,
        "accuracy": accuracy_score(y_true, y_pred),
        "precision": precision_score(y_true, y_pred, zero_division=0),
        "recall": recall_score(y_true, y_pred, zero_division=0),
        "f1_score": f1_score(y_true, y_pred, zero_division=0)
    }

metrics = [
    calculate_metrics("Baseline", y_test, baseline_predictions),
    calculate_metrics("Logistic Regression", y_test, logistic_regression_predictions),
    calculate_metrics("Decision Tree", y_test, decision_tree_predictions),
    calculate_metrics("Random Forest", y_test, random_forest_predictions)
]

metrics_df = pd.DataFrame(metrics)

metrics_df[["accuracy", "precision", "recall", "f1_score"]] = metrics_df[
    ["accuracy", "precision", "recall", "f1_score"]
].round(4)

metrics_df
```

Expected result:

```text
A table should display accuracy, precision, recall, and F1-score for each model.
```

---

## Task 15 - Understand the Metrics

Review the metrics table.

Use the following definitions:

```text
Accuracy  = Overall percentage of correct predictions.
Precision = When the model predicts phishing, how often it is correct.
Recall    = Of all actual phishing websites, how many the model finds.
F1-score  = Balance between precision and recall.
```

In cybersecurity, **recall** is often very important because low recall means the model is missing phishing websites.

> [!knowledge]
> A false negative means the model predicted a website was safe when it was actually phishing. In cybersecurity, false negatives can be dangerous.

---

## Task 16 - Save the Metrics Summary

Run this code:

```python
metrics_summary_file = os.path.join(evaluation_folder, "lab05_metrics_summary.csv")

metrics_df.to_csv(metrics_summary_file, index=False)

print("Saved:", metrics_summary_file)
```

Expected result:

```text
Saved: evaluation\lab05_metrics_summary.csv
```

---

## Task 17 - Create Classification Reports

Run this code:

```python
classification_reports = {
    "baseline_classification_report.txt": classification_report(
        y_test,
        baseline_predictions,
        zero_division=0
    ),
    "logistic_regression_classification_report.txt": classification_report(
        y_test,
        logistic_regression_predictions,
        zero_division=0
    ),
    "decision_tree_classification_report.txt": classification_report(
        y_test,
        decision_tree_predictions,
        zero_division=0
    ),
    "random_forest_classification_report.txt": classification_report(
        y_test,
        random_forest_predictions,
        zero_division=0
    )
}

for file_name, report in classification_reports.items():
    file_path = os.path.join(evaluation_folder, file_name)

    with open(file_path, "w") as file:
        file.write(report)

    print("Saved:", file_path)
```

Expected result:

```text
Four classification report files should be saved.
```

---

## Task 18 - Display the Random Forest Classification Report

Run this code:

```python
print(classification_report(
    y_test,
    random_forest_predictions,
    zero_division=0
))
```

Expected result:

```text
The Random Forest classification report should display.
```

Review the precision, recall, and F1-score for both classes.

---

## Task 19 - Create a Confusion Matrix Function

Run this code:

```python
def save_confusion_matrix(y_true, y_pred, model_name, file_name):
    matrix = confusion_matrix(y_true, y_pred)

    display = ConfusionMatrixDisplay(
        confusion_matrix=matrix,
        display_labels=["Legitimate or Benign", "Phishing or Suspicious"]
    )

    display.plot(values_format="d")

    plt.title(model_name + " Confusion Matrix")
    plt.tight_layout()

    file_path = os.path.join(evaluation_folder, file_name)
    plt.savefig(file_path)
    plt.show()

    print("Saved:", file_path)
    print("Confusion matrix values:")
    print(matrix)
```

Expected result:

```text
The function should be created with no output errors.
```

---

## Task 20 - Create the Logistic Regression Confusion Matrix

Run this code:

```python
save_confusion_matrix(
    y_test,
    logistic_regression_predictions,
    "Logistic Regression",
    "logistic_regression_confusion_matrix.png"
)
```

Expected result:

```text
A confusion matrix chart should display and be saved.
```

---

## Task 21 - Create the Decision Tree Confusion Matrix

Run this code:

```python
save_confusion_matrix(
    y_test,
    decision_tree_predictions,
    "Decision Tree",
    "decision_tree_confusion_matrix.png"
)
```

Expected result:

```text
A confusion matrix chart should display and be saved.
```

---

## Task 22 - Create the Random Forest Confusion Matrix

Run this code:

```python
save_confusion_matrix(
    y_test,
    random_forest_predictions,
    "Random Forest",
    "random_forest_confusion_matrix.png"
)
```

Expected result:

```text
A confusion matrix chart should display and be saved.
```

---

## Task 23 - Interpret Confusion Matrix Values

A confusion matrix has four key outcomes:

```text
True Negative  = Actual legitimate, predicted legitimate
False Positive = Actual legitimate, predicted phishing
False Negative = Actual phishing, predicted legitimate
True Positive  = Actual phishing, predicted phishing
```

In cybersecurity:

```text
False Positive = The model blocks or flags a legitimate website.
False Negative = The model misses a phishing website.
```

False negatives can be especially dangerous because a phishing website may be allowed through.

---

## Task 24 - Calculate False Positives and False Negatives

Run this code:

```python
def confusion_details(model_name, y_true, y_pred):
    tn, fp, fn, tp = confusion_matrix(y_true, y_pred).ravel()

    return {
        "model": model_name,
        "true_negative": tn,
        "false_positive": fp,
        "false_negative": fn,
        "true_positive": tp
    }

confusion_details_df = pd.DataFrame([
    confusion_details("Logistic Regression", y_test, logistic_regression_predictions),
    confusion_details("Decision Tree", y_test, decision_tree_predictions),
    confusion_details("Random Forest", y_test, random_forest_predictions)
])

confusion_details_df
```

Expected result:

```text
A table should display true negatives, false positives, false negatives, and true positives.
```

---

## Task 25 - Save Confusion Details

Run this code:

```python
confusion_details_file = os.path.join(evaluation_folder, "lab05_confusion_details.csv")

confusion_details_df.to_csv(confusion_details_file, index=False)

print("Saved:", confusion_details_file)
```

Expected result:

```text
Saved: evaluation\lab05_confusion_details.csv
```

---

## Task 26 - Create a Model Metrics Comparison Chart

Run this code:

```python
metrics_for_chart = metrics_df.set_index("model")[["accuracy", "precision", "recall", "f1_score"]]

metrics_for_chart.plot(kind="bar", figsize=(10, 6))

plt.title("Model Performance Comparison")
plt.xlabel("Model")
plt.ylabel("Score")
plt.ylim(0, 1)
plt.xticks(rotation=45, ha="right")
plt.tight_layout()

comparison_chart_file = os.path.join(evaluation_folder, "model_metrics_comparison.png")
plt.savefig(comparison_chart_file)
plt.show()

print("Saved:", comparison_chart_file)
```

Expected result:

```text
A bar chart comparing model metrics should display and be saved.
```

---

## Task 27 - Calculate Prediction Probabilities

ROC-AUC uses prediction probabilities instead of only class labels.

Run this code:

```python
logistic_regression_probabilities = logistic_regression_model.predict_proba(X_test_scaled)[:, 1]
decision_tree_probabilities = decision_tree_model.predict_proba(X_test)[:, 1]
random_forest_probabilities = random_forest_model.predict_proba(X_test)[:, 1]

print("Prediction probabilities generated.")
```

Expected result:

```text
Prediction probabilities generated.
```

---

## Task 28 - Calculate ROC-AUC Scores

Run this code:

```python
roc_auc_scores = {
    "Logistic Regression": roc_auc_score(y_test, logistic_regression_probabilities),
    "Decision Tree": roc_auc_score(y_test, decision_tree_probabilities),
    "Random Forest": roc_auc_score(y_test, random_forest_probabilities)
}

for model_name, score in roc_auc_scores.items():
    print(model_name + " ROC-AUC:", round(score, 4))
```

Expected result:

```text
ROC-AUC scores should display for each model.
```

> [!note]
> ROC-AUC measures how well the model separates the two classes across different thresholds. A score closer to 1 is better.

---

## Task 29 - Add ROC-AUC to the Metrics Summary

Run this code:

```python
metrics_df["roc_auc"] = metrics_df["model"].map({
    "Baseline": None,
    "Logistic Regression": roc_auc_scores["Logistic Regression"],
    "Decision Tree": roc_auc_scores["Decision Tree"],
    "Random Forest": roc_auc_scores["Random Forest"]
})

metrics_df["roc_auc"] = metrics_df["roc_auc"].round(4)

metrics_df
```

Expected result:

```text
The metrics table should now include a ROC-AUC column.
```

---

## Task 30 - Save the Updated Metrics Summary

Run this code:

```python
metrics_df.to_csv(metrics_summary_file, index=False)

print("Updated and saved:", metrics_summary_file)
```

Expected result:

```text
Updated and saved: evaluation\lab05_metrics_summary.csv
```

---

## Task 31 - Create ROC Curve Comparison Chart

Run this code:

```python
plt.figure(figsize=(8, 6))

models_for_roc = {
    "Logistic Regression": logistic_regression_probabilities,
    "Decision Tree": decision_tree_probabilities,
    "Random Forest": random_forest_probabilities
}

for model_name, probabilities in models_for_roc.items():
    false_positive_rate, true_positive_rate, thresholds = roc_curve(
        y_test,
        probabilities
    )

    auc_score = roc_auc_score(y_test, probabilities)

    plt.plot(
        false_positive_rate,
        true_positive_rate,
        label=f"{model_name} (AUC = {auc_score:.4f})"
    )

plt.plot([0, 1], [0, 1], linestyle="--", label="Random Guess")

plt.title("ROC Curve Comparison")
plt.xlabel("False Positive Rate")
plt.ylabel("True Positive Rate")
plt.legend()
plt.tight_layout()

roc_chart_file = os.path.join(evaluation_folder, "roc_curve_comparison.png")
plt.savefig(roc_chart_file)
plt.show()

print("Saved:", roc_chart_file)
```

Expected result:

```text
A ROC curve comparison chart should display and be saved.
```

---

## Task 32 - Recommend a Model

For this lab, the recommended model is selected using **F1-score**.

F1-score is useful because it balances precision and recall.

Run this code:

```python
candidate_models = metrics_df[metrics_df["model"] != "Baseline"].copy()

recommended_model_row = candidate_models.sort_values(
    by=["f1_score", "recall", "precision"],
    ascending=False
).iloc[0]

recommended_model = recommended_model_row["model"]

print("Recommended model:", recommended_model)
print("\nRecommended model metrics:")
print(recommended_model_row)
```

Expected result:

```text
The recommended model and its metrics should display.
```

> [!alert]
> In a real cybersecurity environment, you may choose a model with higher recall even if it has slightly lower precision. This is because missing phishing websites can be more dangerous than flagging some safe websites.

---

## Task 33 - Save the Model Recommendation

Run this code:

```python
recommendation_file = os.path.join(evaluation_folder, "lab05_recommended_model.txt")

with open(recommendation_file, "w") as file:
    file.write("Lab 05 - Recommended Model\n")
    file.write("==========================\n")
    file.write("Recommended model: " + str(recommended_model) + "\n\n")
    file.write("Selection method: Highest F1-score, with recall and precision as tie-breakers.\n\n")
    file.write("Metrics:\n")
    file.write(str(recommended_model_row))

print("Saved:", recommendation_file)
```

Expected result:

```text
Saved: evaluation\lab05_recommended_model.txt
```

---

## Task 34 - Save Lab 05 Metadata

Run this code:

```python
metadata_file = os.path.join(evaluation_folder, "lab05_metadata.txt")

with open(metadata_file, "w") as file:
    file.write("Lab 05 - Evaluate Cybersecurity Classification Models\n")
    file.write("Test rows: " + str(X_test.shape[0]) + "\n")
    file.write("Feature columns: " + str(X_test.shape[1]) + "\n")
    file.write("Models evaluated: Baseline, Logistic Regression, Decision Tree, Random Forest\n")
    file.write("Metrics used: accuracy, precision, recall, f1_score, roc_auc\n")
    file.write("Recommended model: " + str(recommended_model) + "\n")
    file.write("Recommended model F1-score: " + str(recommended_model_row["f1_score"]) + "\n")
    file.write("Recommended model recall: " + str(recommended_model_row["recall"]) + "\n")
    file.write("Recommended model precision: " + str(recommended_model_row["precision"]) + "\n")

print("Saved:", metadata_file)
```

Expected result:

```text
Saved: evaluation\lab05_metadata.txt
```

---

## Task 35 - List Evaluation Files

Run this code:

```python
print("Files in evaluation folder:")

for file_name in os.listdir(evaluation_folder):
    print("-", file_name)
```

Expected result:

```text
The evaluation output files should display.
```

---

## Task 36 - Validate Your Work

Run this final validation cell:

```python
required_evaluation_files = [
    "lab05_metrics_summary.csv",
    "lab05_recommended_model.txt",
    "baseline_classification_report.txt",
    "logistic_regression_classification_report.txt",
    "decision_tree_classification_report.txt",
    "random_forest_classification_report.txt",
    "logistic_regression_confusion_matrix.png",
    "decision_tree_confusion_matrix.png",
    "random_forest_confusion_matrix.png",
    "model_metrics_comparison.png",
    "roc_curve_comparison.png",
    "lab05_metadata.txt"
]

for file_name in required_evaluation_files:
    file_path = os.path.join(evaluation_folder, file_name)
    assert os.path.exists(file_path), f"Missing file: {file_name}"

assert X_test.shape == (2211, 30), "X_test should have 2,211 rows and 30 columns."
assert X_test_scaled.shape == (2211, 30), "X_test_scaled should have 2,211 rows and 30 columns."
assert len(y_test) == 2211, "y_test should have 2,211 values."

assert metrics_df.shape[0] == 4, "The metrics table should contain four models."
assert "accuracy" in metrics_df.columns, "Metrics summary should include accuracy."
assert "precision" in metrics_df.columns, "Metrics summary should include precision."
assert "recall" in metrics_df.columns, "Metrics summary should include recall."
assert "f1_score" in metrics_df.columns, "Metrics summary should include f1_score."
assert "roc_auc" in metrics_df.columns, "Metrics summary should include roc_auc."

assert recommended_model in [
    "Logistic Regression",
    "Decision Tree",
    "Random Forest"
], "Recommended model should be one of the trained ML models."

print("Validation passed. Lab 05 is complete.")
```

Expected result:

```text
Validation passed. Lab 05 is complete.
```

---

## Task 37 - Save Your Notebook

1. Select **File**.
2. Select **Save and Checkpoint**.

Confirm the notebook is named:

```text
Lab05-Evaluate-Classification-Models.ipynb
```

---

## Knowledge Check

Answer the following questions:

1. Why is accuracy not enough when evaluating cybersecurity models?
2. What does precision measure?
3. What does recall measure?
4. What does F1-score balance?
5. What is a false positive in this phishing website use case?
6. What is a false negative in this phishing website use case?
7. Why can false negatives be dangerous in cybersecurity?
8. What does a confusion matrix show?
9. What does ROC-AUC measure?
10. Which model did you recommend and why?

---

## Troubleshooting

### Problem: Model Files Are Missing

Run this code:

```python
import os

print(os.getcwd())
print(os.listdir())
```

Check whether the current folder contains:

```text
models
```

If not, check whether Lab 04 contains:

```text
Lab04/models
```

If the model files are missing, return to Lab 04 and complete the validation step.

---

### Problem: Processed Files Are Missing

Check whether the current folder contains:

```text
processed
```

If not, check whether Lab 03 or Lab 04 contains:

```text
processed
```

The required files are:

```text
X_test.csv
y_test.csv
X_test_scaled.csv
```

---

### Problem: `ModuleNotFoundError`

If you see an error such as:

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
3. Run the notebook cells again from the beginning.

---

### Problem: ROC-AUC Calculation Fails

Make sure the model supports probability predictions.

Run:

```python
print(hasattr(random_forest_model, "predict_proba"))
```

Expected result:

```text
True
```

If this returns `False`, retrain the model in Lab 04 using the model definitions provided there.

---

### Problem: Confusion Matrix Labels Look Reversed

This lab uses:

```text
0 = legitimate or benign
1 = phishing or suspicious
```

If your target encoding is different, return to Lab 03 and check the target encoding step.

---

### Problem: Validation Fails Because Shape Is Different

Run:

```python
print(X_test.shape)
print(X_test_scaled.shape)
print(y_test.shape)
```

Expected result:

```text
X_test: (2211, 30)
X_test_scaled: (2211, 30)
y_test: (2211,)
```

If the values are different, return to Lab 03 and recreate the processed files using the original dataset.

---

## Summary

In this lab, you evaluated cybersecurity classification models created in Lab 04.

You completed the following tasks:

- Loaded test data and trained models.
- Generated predictions from each model.
- Calculated accuracy, precision, recall, and F1-score.
- Created classification reports.
- Created confusion matrices.
- Interpreted false positives and false negatives.
- Compared model performance with a chart.
- Calculated ROC-AUC scores.
- Created a ROC curve comparison chart.
- Selected a recommended model.
- Saved evaluation outputs.
- Validated the completed lab.

In Lab 06, you will improve and tune the selected model to see whether performance can be increased.
