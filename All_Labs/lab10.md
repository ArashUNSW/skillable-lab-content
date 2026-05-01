# Lab 10 - Cybersecurity Machine Learning Capstone

## Estimated Time

90 minutes

## Learning Objectives

By the end of this lab, you will be able to:

- Bring together outputs from previous cybersecurity machine learning labs.
- Load a tuned supervised classification model.
- Load the selected classification threshold.
- Load supervised model predictions and unsupervised anomaly detection results.
- Combine model probability, prediction results, and anomaly scores into a risk register.
- Assign risk levels to website records.
- Identify high-risk and critical-risk website examples.
- Review important features from model interpretation.
- Create final charts for reporting.
- Generate an executive summary and technical report.
- Validate all capstone outputs.

---

## Scenario

You are working as a cybersecurity data analyst.

Your team has asked you to prepare a final analysis package for a phishing website detection project.

In previous labs, you completed the following:

- **Lab 01:** Loaded a cybersecurity phishing dataset.
- **Lab 02:** Explored and visualized the dataset.
- **Lab 03:** Prepared the dataset for machine learning.
- **Lab 04:** Trained classification models.
- **Lab 05:** Evaluated model performance.
- **Lab 06:** Tuned and improved the selected model.
- **Lab 08:** Interpreted the model and reviewed feature importance.
- **Lab 09:** Applied unsupervised learning for anomaly detection.

In this capstone lab, you will combine the supervised model, tuned threshold, model interpretation, and unsupervised anomaly detection outputs into a final cybersecurity risk report.

---

## What You Will Create

In this lab, you will create the following files:

```text
capstone/final_risk_register.csv
capstone/high_risk_websites.csv
capstone/critical_risk_websites.csv
capstone/final_model_metrics.csv
capstone/top_feature_importance.csv
capstone/risk_level_distribution.png
capstone/model_probability_distribution.png
capstone/risk_score_vs_anomaly_score.png
capstone/top_feature_importance.png
capstone/final_confusion_matrix.png
capstone/executive_summary.txt
capstone/technical_report.txt
capstone/lab10_metadata.txt
```

These files represent the final project output.

---

## Before You Begin

You should have completed the previous labs.

This lab uses output folders created in earlier labs:

```text
processed
tuning
interpretation
unsupervised
```

The most important required files are:

```text
processed/X_test.csv
processed/y_test.csv
processed/X_test_scaled.csv
tuning/selected_tuned_model.joblib
tuning/selected_threshold.txt
interpretation/model_specific_feature_importance.csv
unsupervised/isolation_forest_results.csv
```

> [!note]
> This lab includes copy logic to find previous lab folders if they are not already in the Lab10 folder.

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

## Task 2 - Create a Lab 10 Folder

In the Jupyter Notebook file browser:

1. Select **New**.
2. Select **Folder**.
3. Select the checkbox beside the new folder.
4. Select **Rename**.
5. Rename the folder to:

```text
Lab10
```

6. Open the **Lab10** folder.

---

## Task 3 - Create a New Python Notebook

1. Inside the **Lab10** folder, select **New**.
2. Select **Python 3 (ipykernel)**.
3. Rename the notebook to:

```text
Lab10-Cybersecurity-ML-Capstone.ipynb
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
import re
import shutil
import pandas as pd
import matplotlib.pyplot as plt
import joblib

from sklearn.linear_model import LogisticRegression
from sklearn.ensemble import RandomForestClassifier
from sklearn.metrics import (
    accuracy_score,
    precision_score,
    recall_score,
    f1_score,
    confusion_matrix,
    ConfusionMatrixDisplay
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
The current Lab10 folder path should display.
```

---

## Task 7 - Locate or Copy Required Folders from Previous Labs

This step looks for the required folders from earlier labs.

Run this code:

```python
processed_folder = "processed"
tuning_folder = "tuning"
interpretation_folder = "interpretation"
unsupervised_folder = "unsupervised"

required_processed_files = [
    "X_train.csv",
    "X_test.csv",
    "y_train.csv",
    "y_test.csv",
    "X_train_scaled.csv",
    "X_test_scaled.csv"
]

required_tuning_files = [
    "selected_tuned_model.joblib",
    "selected_threshold.txt"
]

required_interpretation_files = [
    "model_specific_feature_importance.csv"
]

required_unsupervised_files = [
    "isolation_forest_results.csv"
]

def required_files_exist(folder, required_files):
    return os.path.isdir(folder) and all(
        os.path.exists(os.path.join(folder, file_name))
        for file_name in required_files
    )

def copy_folder_if_available(destination_folder, possible_source_folders, required_files):
    if required_files_exist(destination_folder, required_files):
        print(f"{destination_folder} folder found in the current Lab10 folder.")
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
        os.path.join("..", "Lab09", "processed"),
        os.path.join("..", "Lab08", "processed"),
        os.path.join("..", "Lab06", "processed"),
        os.path.join("..", "Lab05", "processed"),
        os.path.join("..", "Lab04", "processed"),
        os.path.join("..", "Lab03", "processed")
    ],
    required_processed_files
)

tuning_found = copy_folder_if_available(
    tuning_folder,
    [
        os.path.join("..", "Lab08", "tuning"),
        os.path.join("..", "Lab06", "tuning")
    ],
    required_tuning_files
)

interpretation_found = copy_folder_if_available(
    interpretation_folder,
    [
        os.path.join("..", "Lab08", "interpretation")
    ],
    required_interpretation_files
)

unsupervised_found = copy_folder_if_available(
    unsupervised_folder,
    [
        os.path.join("..", "Lab09", "unsupervised")
    ],
    required_unsupervised_files
)

print("\nProcessed files ready:", processed_found)
print("Tuning files ready:", tuning_found)
print("Interpretation files ready:", interpretation_found)
print("Unsupervised files ready:", unsupervised_found)
```

Expected result:

```text
Processed files ready: True
Tuning files ready: True
Interpretation files ready: True
Unsupervised files ready: True
```

> [!alert]
> If any folder is not found, return to the related previous lab and complete the validation step.

---

## Task 8 - Create a Capstone Output Folder

Run this code:

```python
capstone_folder = "capstone"

os.makedirs(capstone_folder, exist_ok=True)

print("Capstone output folder created:", capstone_folder)
```

Expected result:

```text
Capstone output folder created: capstone
```

---

## Task 9 - Load the Processed Test Data

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

## Task 10 - Load the Selected Tuned Model

Run this code:

```python
selected_model_file = os.path.join(tuning_folder, "selected_tuned_model.joblib")

selected_model = joblib.load(selected_model_file)

print("Selected tuned model loaded.")
print("Model type:", type(selected_model))
```

Expected result:

```text
Selected tuned model loaded.
```

---

## Task 11 - Identify Whether the Model Uses Scaled Data

Run this code:

```python
if isinstance(selected_model, LogisticRegression):
    selected_model_name = "Tuned Logistic Regression"
    selected_model_uses_scaled_data = True

elif isinstance(selected_model, RandomForestClassifier):
    selected_model_name = "Tuned Random Forest"
    selected_model_uses_scaled_data = False

else:
    selected_model_name = type(selected_model).__name__
    selected_model_uses_scaled_data = False

print("Selected model:", selected_model_name)
print("Uses scaled data:", selected_model_uses_scaled_data)
```

Expected result:

```text
The selected model name and scaled-data requirement should display.
```

---

## Task 12 - Load the Selected Threshold

Run this code:

```python
selected_threshold_file = os.path.join(tuning_folder, "selected_threshold.txt")

with open(selected_threshold_file, "r") as file:
    threshold_text = file.read()

threshold_match = re.search(r"Selected threshold:\s*([0-9.]+)", threshold_text)

if threshold_match:
    selected_threshold = float(threshold_match.group(1))
else:
    selected_threshold = 0.50

print("Selected threshold:", selected_threshold)
```

Expected result:

```text
The selected threshold should display.
```

---

## Task 13 - Select the Correct Test Dataset for Prediction

Run this code:

```python
if selected_model_uses_scaled_data:
    X_test_for_model = X_test_scaled
else:
    X_test_for_model = X_test

print("X_test_for_model shape:", X_test_for_model.shape)
```

Expected result:

```text
X_test_for_model shape: (2211, 30)
```

---

## Task 14 - Generate Final Supervised Predictions

Run this code:

```python
phishing_probabilities = selected_model.predict_proba(X_test_for_model)[:, 1]

supervised_predictions = (
    phishing_probabilities >= selected_threshold
).astype(int)

print("Generated supervised predictions.")
print("Number of predictions:", len(supervised_predictions))
```

Expected result:

```text
Generated supervised predictions.
Number of predictions: 2211
```

In this lab:

```text
1 = phishing or suspicious
0 = legitimate or benign
```

---

## Task 15 - Calculate Final Supervised Model Metrics

Run this code:

```python
final_metrics = {
    "model": selected_model_name,
    "threshold": selected_threshold,
    "accuracy": accuracy_score(y_test, supervised_predictions),
    "precision": precision_score(y_test, supervised_predictions, zero_division=0),
    "recall": recall_score(y_test, supervised_predictions, zero_division=0),
    "f1_score": f1_score(y_test, supervised_predictions, zero_division=0)
}

final_metrics_df = pd.DataFrame([final_metrics])

final_metrics_df[["accuracy", "precision", "recall", "f1_score"]] = final_metrics_df[
    ["accuracy", "precision", "recall", "f1_score"]
].round(4)

final_metrics_df
```

Expected result:

```text
A table with final supervised model metrics should display.
```

---

## Task 16 - Save the Final Model Metrics

Run this code:

```python
final_metrics_file = os.path.join(
    capstone_folder,
    "final_model_metrics.csv"
)

final_metrics_df.to_csv(final_metrics_file, index=False)

print("Saved:", final_metrics_file)
```

Expected result:

```text
Saved: capstone\final_model_metrics.csv
```

---

## Task 17 - Create a Final Confusion Matrix

Run this code:

```python
final_matrix = confusion_matrix(y_test, supervised_predictions)

display = ConfusionMatrixDisplay(
    confusion_matrix=final_matrix,
    display_labels=["Legitimate or Benign", "Phishing or Suspicious"]
)

display.plot(values_format="d")

plt.title("Final Model Confusion Matrix")
plt.tight_layout()

final_confusion_matrix_file = os.path.join(
    capstone_folder,
    "final_confusion_matrix.png"
)

plt.savefig(final_confusion_matrix_file)
plt.show()

print("Saved:", final_confusion_matrix_file)
print(final_matrix)
```

Expected result:

```text
A confusion matrix should display and be saved.
```

---

## Task 18 - Load Unsupervised Anomaly Results

The unsupervised results from Lab 09 include all rows from the original dataset.

In Lab 09, the combined dataset was created in this order:

```text
training rows first
testing rows second
```

Because of that, the Lab 10 test rows start after the training rows.

Run this code:

```python
isolation_results_file = os.path.join(
    unsupervised_folder,
    "isolation_forest_results.csv"
)

isolation_results_full = pd.read_csv(isolation_results_file)

test_start_index = len(X_train)
test_end_index = test_start_index + len(X_test)

isolation_results_test = isolation_results_full.iloc[
    test_start_index:test_end_index
].reset_index(drop=True)

print("Full unsupervised results shape:", isolation_results_full.shape)
print("Test unsupervised results shape:", isolation_results_test.shape)

isolation_results_test[["known_target", "anomaly_flag", "anomaly_score"]].head()
```

Expected result:

```text
Test unsupervised results shape: (2211, ...)
```

---

## Task 19 - Normalize the Anomaly Score

Run this code:

```python
anomaly_score = isolation_results_test["anomaly_score"]

anomaly_score_min = anomaly_score.min()
anomaly_score_max = anomaly_score.max()

normalized_anomaly_score = (
    (anomaly_score - anomaly_score_min) /
    (anomaly_score_max - anomaly_score_min)
)

print("Anomaly score minimum:", round(anomaly_score_min, 4))
print("Anomaly score maximum:", round(anomaly_score_max, 4))
print("Normalized anomaly score range:")
print(normalized_anomaly_score.min(), normalized_anomaly_score.max())
```

Expected result:

```text
Normalized anomaly score range should be from 0.0 to 1.0.
```

---

## Task 20 - Build a Final Risk Register

A risk register is a structured table that helps analysts prioritize records for investigation.

This lab creates a risk score using:

```text
75% supervised phishing probability
25% normalized anomaly score
```

Run this code:

```python
risk_register = X_test.copy()

risk_register["actual_target"] = y_test.values
risk_register["phishing_probability"] = phishing_probabilities
risk_register["supervised_prediction"] = supervised_predictions
risk_register["anomaly_flag"] = isolation_results_test["anomaly_flag"].values
risk_register["anomaly_score"] = isolation_results_test["anomaly_score"].values
risk_register["normalized_anomaly_score"] = normalized_anomaly_score.values

risk_register["final_risk_score"] = (
    0.75 * risk_register["phishing_probability"] +
    0.25 * risk_register["normalized_anomaly_score"]
)

risk_register.head()
```

Expected result:

```text
A risk register table should display.
```

---

## Task 21 - Assign Risk Levels

Run this code:

```python
def assign_risk_level(score):
    if score >= 0.80:
        return "Critical"
    elif score >= 0.60:
        return "High"
    elif score >= 0.40:
        return "Medium"
    else:
        return "Low"

risk_register["risk_level"] = risk_register["final_risk_score"].apply(assign_risk_level)

risk_register["risk_level"].value_counts()
```

Expected result:

```text
A count of records in each risk level should display.
```

---

## Task 22 - Add Recommended Analyst Action

Run this code:

```python
def recommend_action(row):
    if row["risk_level"] == "Critical":
        return "Block or investigate immediately"
    elif row["risk_level"] == "High":
        return "Prioritize for analyst review"
    elif row["supervised_prediction"] == 1:
        return "Review as model-flagged phishing"
    elif row["anomaly_flag"] == 1:
        return "Review as unusual behavior"
    else:
        return "Monitor"

risk_register["recommended_action"] = risk_register.apply(
    recommend_action,
    axis=1
)

risk_register[[
    "actual_target",
    "phishing_probability",
    "anomaly_flag",
    "final_risk_score",
    "risk_level",
    "recommended_action"
]].head()
```

Expected result:

```text
The risk register should include recommended analyst actions.
```

---

## Task 23 - Sort the Risk Register by Risk Score

Run this code:

```python
risk_register = risk_register.sort_values(
    by="final_risk_score",
    ascending=False
).reset_index(drop=True)

risk_register[[
    "actual_target",
    "phishing_probability",
    "anomaly_score",
    "final_risk_score",
    "risk_level",
    "recommended_action"
]].head(10)
```

Expected result:

```text
The top 10 highest-risk records should display.
```

---

## Task 24 - Save the Final Risk Register

Run this code:

```python
final_risk_register_file = os.path.join(
    capstone_folder,
    "final_risk_register.csv"
)

risk_register.to_csv(final_risk_register_file, index=False)

print("Saved:", final_risk_register_file)
```

Expected result:

```text
Saved: capstone\final_risk_register.csv
```

---

## Task 25 - Save High-Risk Website Records

Run this code:

```python
high_risk_websites = risk_register[
    risk_register["risk_level"].isin(["High", "Critical"])
].copy()

high_risk_file = os.path.join(
    capstone_folder,
    "high_risk_websites.csv"
)

high_risk_websites.to_csv(high_risk_file, index=False)

print("High and critical risk records:", len(high_risk_websites))
print("Saved:", high_risk_file)
```

Expected result:

```text
High and critical risk records should be saved.
```

---

## Task 26 - Save Critical-Risk Website Records

Run this code:

```python
critical_risk_websites = risk_register[
    risk_register["risk_level"] == "Critical"
].copy()

critical_risk_file = os.path.join(
    capstone_folder,
    "critical_risk_websites.csv"
)

critical_risk_websites.to_csv(critical_risk_file, index=False)

print("Critical risk records:", len(critical_risk_websites))
print("Saved:", critical_risk_file)
```

Expected result:

```text
Critical risk records should be saved.
```

---

## Task 27 - Load Model Feature Importance

Run this code:

```python
feature_importance_file = os.path.join(
    interpretation_folder,
    "model_specific_feature_importance.csv"
)

feature_importance_df = pd.read_csv(feature_importance_file)

top_feature_importance = feature_importance_df.head(10).copy()

top_feature_importance
```

Expected result:

```text
The top 10 important features should display.
```

---

## Task 28 - Save Top Feature Importance

Run this code:

```python
top_feature_importance_file = os.path.join(
    capstone_folder,
    "top_feature_importance.csv"
)

top_feature_importance.to_csv(top_feature_importance_file, index=False)

print("Saved:", top_feature_importance_file)
```

Expected result:

```text
Saved: capstone\top_feature_importance.csv
```

---

## Task 29 - Create a Risk Level Distribution Chart

Run this code:

```python
risk_order = ["Low", "Medium", "High", "Critical"]

risk_counts = risk_register["risk_level"].value_counts().reindex(
    risk_order,
    fill_value=0
)

plt.figure(figsize=(7, 5))

risk_counts.plot(kind="bar")

plt.title("Final Risk Level Distribution")
plt.xlabel("Risk Level")
plt.ylabel("Number of Website Records")
plt.xticks(rotation=0)
plt.tight_layout()

risk_distribution_chart_file = os.path.join(
    capstone_folder,
    "risk_level_distribution.png"
)

plt.savefig(risk_distribution_chart_file)
plt.show()

print("Saved:", risk_distribution_chart_file)
```

Expected result:

```text
A risk level distribution chart should display and be saved.
```

---

## Task 30 - Create a Model Probability Distribution Chart

Run this code:

```python
plt.figure(figsize=(8, 5))

plt.hist(
    risk_register["phishing_probability"],
    bins=20
)

plt.title("Model Phishing Probability Distribution")
plt.xlabel("Predicted Phishing Probability")
plt.ylabel("Number of Website Records")
plt.tight_layout()

probability_chart_file = os.path.join(
    capstone_folder,
    "model_probability_distribution.png"
)

plt.savefig(probability_chart_file)
plt.show()

print("Saved:", probability_chart_file)
```

Expected result:

```text
A model probability distribution chart should display and be saved.
```

---

## Task 31 - Create a Risk Score vs. Anomaly Score Chart

Run this code:

```python
plt.figure(figsize=(8, 6))

plt.scatter(
    risk_register["normalized_anomaly_score"],
    risk_register["final_risk_score"],
    s=15,
    alpha=0.6
)

plt.title("Risk Score vs. Normalized Anomaly Score")
plt.xlabel("Normalized Anomaly Score")
plt.ylabel("Final Risk Score")
plt.tight_layout()

risk_vs_anomaly_chart_file = os.path.join(
    capstone_folder,
    "risk_score_vs_anomaly_score.png"
)

plt.savefig(risk_vs_anomaly_chart_file)
plt.show()

print("Saved:", risk_vs_anomaly_chart_file)
```

Expected result:

```text
A scatter plot should display and be saved.
```

---

## Task 32 - Create a Top Feature Importance Chart

Run this code:

```python
top_features_for_chart = top_feature_importance.sort_values(
    by="importance",
    ascending=True
)

plt.figure(figsize=(10, 6))

plt.barh(
    top_features_for_chart["feature"],
    top_features_for_chart["importance"]
)

plt.title("Top 10 Feature Importance")
plt.xlabel("Importance")
plt.ylabel("Feature")
plt.tight_layout()

feature_importance_chart_file = os.path.join(
    capstone_folder,
    "top_feature_importance.png"
)

plt.savefig(feature_importance_chart_file)
plt.show()

print("Saved:", feature_importance_chart_file)
```

Expected result:

```text
A top feature importance chart should display and be saved.
```

---

## Task 33 - Calculate Final Investigation Counts

Run this code:

```python
total_records = len(risk_register)
critical_count = len(critical_risk_websites)
high_count = len(risk_register[risk_register["risk_level"] == "High"])
medium_count = len(risk_register[risk_register["risk_level"] == "Medium"])
low_count = len(risk_register[risk_register["risk_level"] == "Low"])

model_flagged_count = int(risk_register["supervised_prediction"].sum())
anomaly_flagged_count = int(risk_register["anomaly_flag"].sum())

true_phishing_count = int(risk_register["actual_target"].sum())

print("Total records:", total_records)
print("True phishing or suspicious records:", true_phishing_count)
print("Model-flagged records:", model_flagged_count)
print("Anomaly-flagged records:", anomaly_flagged_count)
print("Critical risk records:", critical_count)
print("High risk records:", high_count)
print("Medium risk records:", medium_count)
print("Low risk records:", low_count)
```

Expected result:

```text
Final investigation counts should display.
```

---

## Task 34 - Create an Executive Summary

Run this code:

```python
executive_summary_file = os.path.join(
    capstone_folder,
    "executive_summary.txt"
)

with open(executive_summary_file, "w") as file:
    file.write("Cybersecurity Machine Learning Capstone - Executive Summary\n")
    file.write("=========================================================\n\n")

    file.write("Project Overview\n")
    file.write("----------------\n")
    file.write("This project used machine learning to analyze website records and identify potential phishing or suspicious websites.\n\n")

    file.write("Final Model\n")
    file.write("-----------\n")
    file.write("Selected model: " + str(selected_model_name) + "\n")
    file.write("Selected threshold: " + str(selected_threshold) + "\n\n")

    file.write("Final Model Metrics\n")
    file.write("-------------------\n")
    file.write("Accuracy: " + str(final_metrics_df.loc[0, "accuracy"]) + "\n")
    file.write("Precision: " + str(final_metrics_df.loc[0, "precision"]) + "\n")
    file.write("Recall: " + str(final_metrics_df.loc[0, "recall"]) + "\n")
    file.write("F1-score: " + str(final_metrics_df.loc[0, "f1_score"]) + "\n\n")

    file.write("Risk Summary\n")
    file.write("------------\n")
    file.write("Total records reviewed: " + str(total_records) + "\n")
    file.write("Critical risk records: " + str(critical_count) + "\n")
    file.write("High risk records: " + str(high_count) + "\n")
    file.write("Medium risk records: " + str(medium_count) + "\n")
    file.write("Low risk records: " + str(low_count) + "\n\n")

    file.write("Recommended Analyst Action\n")
    file.write("--------------------------\n")
    file.write("Critical records should be blocked or investigated immediately.\n")
    file.write("High-risk records should be prioritized for analyst review.\n")
    file.write("Medium-risk records should be reviewed if time and resources allow.\n")
    file.write("Low-risk records can be monitored.\n\n")

    file.write("Important Note\n")
    file.write("--------------\n")
    file.write("Machine learning results should support analyst decision-making. They should not replace human security review.\n")

print("Saved:", executive_summary_file)
```

Expected result:

```text
Saved: capstone\executive_summary.txt
```

---

## Task 35 - Create a Technical Report

Run this code:

```python
top_features_text = "\n".join(
    [
        "- " + row["feature"] + ": " + str(round(row["importance"], 4))
        for _, row in top_feature_importance.iterrows()
    ]
)

technical_report_file = os.path.join(
    capstone_folder,
    "technical_report.txt"
)

with open(technical_report_file, "w") as file:
    file.write("Cybersecurity Machine Learning Capstone - Technical Report\n")
    file.write("=========================================================\n\n")

    file.write("Data\n")
    file.write("----\n")
    file.write("Test rows: " + str(X_test.shape[0]) + "\n")
    file.write("Feature columns: " + str(X_test.shape[1]) + "\n")
    file.write("Target labels: 0 = legitimate or benign, 1 = phishing or suspicious\n\n")

    file.write("Supervised Model\n")
    file.write("----------------\n")
    file.write("Selected model: " + str(selected_model_name) + "\n")
    file.write("Uses scaled data: " + str(selected_model_uses_scaled_data) + "\n")
    file.write("Selected threshold: " + str(selected_threshold) + "\n")
    file.write("Accuracy: " + str(final_metrics_df.loc[0, "accuracy"]) + "\n")
    file.write("Precision: " + str(final_metrics_df.loc[0, "precision"]) + "\n")
    file.write("Recall: " + str(final_metrics_df.loc[0, "recall"]) + "\n")
    file.write("F1-score: " + str(final_metrics_df.loc[0, "f1_score"]) + "\n\n")

    file.write("Unsupervised Anomaly Detection\n")
    file.write("------------------------------\n")
    file.write("Anomaly flagged records in test set: " + str(anomaly_flagged_count) + "\n")
    file.write("Anomaly score was normalized and combined with supervised model probability.\n\n")

    file.write("Risk Scoring Method\n")
    file.write("-------------------\n")
    file.write("Final risk score = 0.75 * phishing probability + 0.25 * normalized anomaly score\n")
    file.write("Critical: score >= 0.80\n")
    file.write("High: score >= 0.60 and < 0.80\n")
    file.write("Medium: score >= 0.40 and < 0.60\n")
    file.write("Low: score < 0.40\n\n")

    file.write("Risk Register Summary\n")
    file.write("---------------------\n")
    file.write("Critical risk records: " + str(critical_count) + "\n")
    file.write("High risk records: " + str(high_count) + "\n")
    file.write("Medium risk records: " + str(medium_count) + "\n")
    file.write("Low risk records: " + str(low_count) + "\n\n")

    file.write("Top 10 Important Features\n")
    file.write("-------------------------\n")
    file.write(top_features_text + "\n\n")

    file.write("Output Files\n")
    file.write("------------\n")
    file.write("final_risk_register.csv\n")
    file.write("high_risk_websites.csv\n")
    file.write("critical_risk_websites.csv\n")
    file.write("final_model_metrics.csv\n")
    file.write("top_feature_importance.csv\n")
    file.write("risk_level_distribution.png\n")
    file.write("model_probability_distribution.png\n")
    file.write("risk_score_vs_anomaly_score.png\n")
    file.write("top_feature_importance.png\n")
    file.write("final_confusion_matrix.png\n")

print("Saved:", technical_report_file)
```

Expected result:

```text
Saved: capstone\technical_report.txt
```

---

## Task 36 - Create Lab 10 Metadata

Run this code:

```python
metadata_file = os.path.join(
    capstone_folder,
    "lab10_metadata.txt"
)

with open(metadata_file, "w") as file:
    file.write("Lab 10 - Cybersecurity Machine Learning Capstone\n")
    file.write("Test rows: " + str(X_test.shape[0]) + "\n")
    file.write("Feature columns: " + str(X_test.shape[1]) + "\n")
    file.write("Selected model: " + str(selected_model_name) + "\n")
    file.write("Selected threshold: " + str(selected_threshold) + "\n")
    file.write("Uses scaled data: " + str(selected_model_uses_scaled_data) + "\n")
    file.write("Risk score formula: 0.75 * phishing_probability + 0.25 * normalized_anomaly_score\n")
    file.write("Critical count: " + str(critical_count) + "\n")
    file.write("High count: " + str(high_count) + "\n")
    file.write("Medium count: " + str(medium_count) + "\n")
    file.write("Low count: " + str(low_count) + "\n")
    file.write("Accuracy: " + str(final_metrics_df.loc[0, "accuracy"]) + "\n")
    file.write("Precision: " + str(final_metrics_df.loc[0, "precision"]) + "\n")
    file.write("Recall: " + str(final_metrics_df.loc[0, "recall"]) + "\n")
    file.write("F1-score: " + str(final_metrics_df.loc[0, "f1_score"]) + "\n")

print("Saved:", metadata_file)
```

Expected result:

```text
Saved: capstone\lab10_metadata.txt
```

---

## Task 37 - List Capstone Output Files

Run this code:

```python
print("Files in capstone folder:")

for file_name in os.listdir(capstone_folder):
    print("-", file_name)
```

Expected result:

```text
The capstone output files should display.
```

You should see files such as:

```text
final_risk_register.csv
high_risk_websites.csv
critical_risk_websites.csv
final_model_metrics.csv
top_feature_importance.csv
risk_level_distribution.png
model_probability_distribution.png
risk_score_vs_anomaly_score.png
top_feature_importance.png
final_confusion_matrix.png
executive_summary.txt
technical_report.txt
lab10_metadata.txt
```

---

## Task 38 - Review the Final Risk Register

Run this code:

```python
risk_register[[
    "actual_target",
    "phishing_probability",
    "supervised_prediction",
    "anomaly_flag",
    "final_risk_score",
    "risk_level",
    "recommended_action"
]].head(20)
```

Expected result:

```text
The top 20 highest-risk records should display.
```

Use this table to review which records would be prioritized for cybersecurity analyst investigation.

---

## Task 39 - Validate Your Work

Run this final validation cell:

```python
required_capstone_files = [
    "final_risk_register.csv",
    "high_risk_websites.csv",
    "critical_risk_websites.csv",
    "final_model_metrics.csv",
    "top_feature_importance.csv",
    "risk_level_distribution.png",
    "model_probability_distribution.png",
    "risk_score_vs_anomaly_score.png",
    "top_feature_importance.png",
    "final_confusion_matrix.png",
    "executive_summary.txt",
    "technical_report.txt",
    "lab10_metadata.txt"
]

for file_name in required_capstone_files:
    file_path = os.path.join(capstone_folder, file_name)
    assert os.path.exists(file_path), f"Missing file: {file_name}"

assert X_test.shape == (2211, 30), "X_test should have 2,211 rows and 30 columns."
assert len(y_test) == 2211, "y_test should have 2,211 values."
assert len(phishing_probabilities) == 2211, "There should be 2,211 phishing probabilities."
assert len(supervised_predictions) == 2211, "There should be 2,211 supervised predictions."
assert len(risk_register) == 2211, "The risk register should contain 2,211 records."

assert "final_risk_score" in risk_register.columns, "Risk register should contain final_risk_score."
assert "risk_level" in risk_register.columns, "Risk register should contain risk_level."
assert "recommended_action" in risk_register.columns, "Risk register should contain recommended_action."

assert final_metrics_df.loc[0, "accuracy"] >= 0, "Accuracy should be valid."
assert final_metrics_df.loc[0, "precision"] >= 0, "Precision should be valid."
assert final_metrics_df.loc[0, "recall"] >= 0, "Recall should be valid."
assert final_metrics_df.loc[0, "f1_score"] >= 0, "F1-score should be valid."

assert final_metrics_df.loc[0, "accuracy"] <= 1, "Accuracy should be less than or equal to 1."
assert final_metrics_df.loc[0, "precision"] <= 1, "Precision should be less than or equal to 1."
assert final_metrics_df.loc[0, "recall"] <= 1, "Recall should be less than or equal to 1."
assert final_metrics_df.loc[0, "f1_score"] <= 1, "F1-score should be less than or equal to 1."

print("Validation passed. Lab 10 is complete.")
```

Expected result:

```text
Validation passed. Lab 10 is complete.
```

---

## Task 40 - Save Your Notebook

1. Select **File**.
2. Select **Save and Checkpoint**.

Confirm the notebook is named:

```text
Lab10-Cybersecurity-ML-Capstone.ipynb
```

---

## Final Knowledge Check

Answer the following questions:

1. What was the final selected supervised model?
2. What classification threshold was used?
3. Why did the capstone combine supervised model probability with anomaly score?
4. What does the final risk score represent?
5. Which risk level should be investigated first?
6. What is the difference between a high-risk record and a critical-risk record?
7. Why is recall important in phishing detection?
8. Why might false negatives be dangerous in cybersecurity?
9. Why should machine learning support, not replace, analyst judgment?
10. Which files would you share with a security manager?
11. Which files would you share with a technical machine learning team?
12. What improvements could be made in a future version of this project?

---

## Troubleshooting

### Problem: Required Folders Are Missing

Run this code:

```python
import os

print(os.getcwd())
print(os.listdir())
```

Check whether the current folder contains:

```text
processed
tuning
interpretation
unsupervised
```

If a folder is missing, check previous lab folders:

```text
../Lab03/processed
../Lab06/tuning
../Lab08/interpretation
../Lab09/unsupervised
```

If the files are missing from those locations, return to the related lab and complete the validation step.

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
3. Run the notebook again from the beginning.

---

### Problem: Selected Threshold Cannot Be Read

If the threshold file cannot be parsed, the notebook uses:

```text
0.50
```

To check the threshold file manually, run:

```python
with open("tuning/selected_threshold.txt", "r") as file:
    print(file.read())
```

---

### Problem: Risk Register Has Fewer Than 2,211 Rows

Run:

```python
print(X_test.shape)
print(len(y_test))
print(len(risk_register))
```

Expected values:

```text
X_test shape: (2211, 30)
y_test length: 2211
risk_register length: 2211
```

If values are different, return to Lab 03 and recreate the processed files.

---

### Problem: Unsupervised Results Do Not Align with Test Rows

This lab assumes Lab 09 combined the data in this order:

```text
X_train first
X_test second
```

The test data starts at this index:

```python
test_start_index = len(X_train)
```

If you changed Lab 09, recreate the unsupervised results using the Lab 09 instructions.

---

### Problem: No Critical Records Are Found

This is possible depending on the selected model, threshold, and score distribution.

The validation does not require a specific number of critical records.

Review the high-risk records instead:

```python
high_risk_websites.head()
```

---

## Summary

In this final capstone lab, you brought together the full cybersecurity machine learning workflow.

You completed the following tasks:

- Loaded processed test data.
- Loaded the selected tuned model.
- Loaded the selected classification threshold.
- Generated final supervised predictions.
- Calculated final model metrics.
- Created a final confusion matrix.
- Loaded unsupervised anomaly detection results.
- Combined phishing probability and anomaly score.
- Built a final cybersecurity risk register.
- Assigned risk levels.
- Added recommended analyst actions.
- Saved high-risk and critical-risk records.
- Loaded feature importance results.
- Created final reporting charts.
- Generated an executive summary.
- Generated a technical report.
- Validated all final capstone outputs.

You have completed the full cybersecurity machine learning lab series.
