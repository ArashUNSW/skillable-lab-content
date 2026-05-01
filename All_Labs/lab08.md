# Lab 08 - Interpret Model and Feature Importance

## Estimated Time

75 minutes

## Learning Objectives

By the end of this lab, you will be able to:

- Continue from the tuned model created in Lab 06.
- Load the selected cybersecurity classification model.
- Load the selected classification threshold.
- Understand why model interpretation matters in cybersecurity.
- Identify the most important website features used by the model.
- Compare model-specific feature importance with permutation importance.
- Review false positives and false negatives.
- Interpret model behavior from a cybersecurity point of view.
- Save feature importance reports, charts, and error analysis files.
- Prepare interpretation outputs for later reporting or deployment labs.

---

## Scenario

You are working as a cybersecurity data analyst.

In previous labs, you completed the following:

- **Lab 01:** Loaded a cybersecurity phishing dataset.
- **Lab 02:** Explored and visualized the dataset.
- **Lab 03:** Prepared the dataset for machine learning.
- **Lab 04:** Trained classification models.
- **Lab 05:** Evaluated the models.
- **Lab 06:** Tuned and improved the selected model.

Now you need to explain what the model learned.

A cybersecurity model should not only make predictions. Analysts also need to understand which features influence the model and where the model makes mistakes.

In this lab, you will interpret the tuned model, identify important features, analyze false positives and false negatives, and save interpretation artifacts.

---

## What You Will Create

In this lab, you will create the following files:

```text
interpretation/model_specific_feature_importance.csv
interpretation/model_specific_feature_importance.png
interpretation/permutation_importance.csv
interpretation/permutation_importance.png
interpretation/top_features_by_target.csv
interpretation/threshold_confusion_matrix.png
interpretation/error_analysis.csv
interpretation/false_positive_examples.csv
interpretation/false_negative_examples.csv
interpretation/lab08_interpretation_summary.txt
interpretation/lab08_metadata.txt
```

These files document how the model behaves and which features appear most important.

---

## Before You Begin

You should have completed Lab 06.

Lab 06 created a folder named:

```text
tuning
```

The `tuning` folder should contain:

```text
selected_tuned_model.joblib
selected_threshold.txt
final_threshold_predictions.csv
lab06_metadata.txt
```

You also need the processed data files created in Lab 03:

```text
processed/X_train.csv
processed/X_test.csv
processed/y_train.csv
processed/y_test.csv
processed/X_train_scaled.csv
processed/X_test_scaled.csv
```

> [!note]
> This lab includes copy logic to find the required folders from previous labs if they are not already in the Lab08 folder.

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

## Task 2 - Create a Lab 08 Folder

In the Jupyter Notebook file browser:

1. Select **New**.
2. Select **Folder**.
3. Select the checkbox beside the new folder.
4. Select **Rename**.
5. Rename the folder to:

```text
Lab08
```

6. Open the **Lab08** folder.

---

## Task 3 - Create a New Python Notebook

1. Inside the **Lab08** folder, select **New**.
2. Select **Python 3 (ipykernel)**.
3. Rename the notebook to:

```text
Lab08-Interpret-Model-Feature-Importance.ipynb
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
from sklearn.metrics import confusion_matrix, ConfusionMatrixDisplay
from sklearn.inspection import permutation_importance
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
The current Lab08 folder path should display.
```

---

## Task 7 - Locate or Copy Required Files from Previous Labs

This step looks for the `processed` and `tuning` folders.

If they are not in the Lab08 folder, the code attempts to copy them from previous lab folders.

Run this code:

```python
processed_folder = "processed"
tuning_folder = "tuning"

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
    "selected_threshold.txt",
    "final_threshold_predictions.csv",
    "lab06_metadata.txt"
]

def required_files_exist(folder, required_files):
    return os.path.isdir(folder) and all(
        os.path.exists(os.path.join(folder, file_name))
        for file_name in required_files
    )

def copy_folder_if_available(destination_folder, possible_source_folders, required_files):
    if required_files_exist(destination_folder, required_files):
        print(f"{destination_folder} folder found in the current Lab08 folder.")
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
        os.path.join("..", "Lab06", "tuning")
    ],
    required_tuning_files
)

print("\nProcessed files ready:", processed_found)
print("Tuning files ready:", tuning_found)
```

Expected result:

```text
Processed files ready: True
Tuning files ready: True
```

> [!alert]
> If the tuning files are not found, return to Lab 06 and complete the validation step.

---

## Task 8 - Create an Interpretation Output Folder

Run this code:

```python
interpretation_folder = "interpretation"

os.makedirs(interpretation_folder, exist_ok=True)

print("Interpretation folder created:", interpretation_folder)
```

Expected result:

```text
Interpretation folder created: interpretation
```

---

## Task 9 - Load the Processed Data

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

print("Selected tuned model loaded successfully.")
print("Model type:", type(selected_model))
```

Expected result:

```text
The selected tuned model should load successfully.
```

---

## Task 11 - Identify the Model Type

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

print("Selected model name:", selected_model_name)
print("Uses scaled data:", selected_model_uses_scaled_data)
```

Expected result:

```text
The selected model name and data type requirement should display.
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
The selected threshold from Lab 06 should display.
```

> [!note]
> If the threshold cannot be read, the code uses a default threshold of `0.50`.

---

## Task 13 - Select the Correct Test Data for the Model

Logistic Regression uses scaled data.

Random Forest uses unscaled data.

Run this code:

```python
if selected_model_uses_scaled_data:
    X_test_for_model = X_test_scaled
    X_train_for_model = X_train_scaled
else:
    X_test_for_model = X_test
    X_train_for_model = X_train

print("X_test_for_model shape:", X_test_for_model.shape)
print("X_train_for_model shape:", X_train_for_model.shape)
```

Expected result:

```text
The selected model input data should have 30 feature columns.
```

---

## Task 14 - Generate Probability-Based Predictions

Run this code:

```python
phishing_probabilities = selected_model.predict_proba(X_test_for_model)[:, 1]

threshold_predictions = (phishing_probabilities >= selected_threshold).astype(int)

print("Generated predictions using selected threshold:", selected_threshold)
print("Number of predictions:", len(threshold_predictions))
```

Expected result:

```text
Generated predictions using selected threshold.
Number of predictions: 2211
```

---

## Task 15 - Create a Threshold Confusion Matrix

Run this code:

```python
threshold_matrix = confusion_matrix(y_test, threshold_predictions)

display = ConfusionMatrixDisplay(
    confusion_matrix=threshold_matrix,
    display_labels=["Legitimate or Benign", "Phishing or Suspicious"]
)

display.plot(values_format="d")

plt.title("Selected Model Confusion Matrix with Tuned Threshold")
plt.tight_layout()

confusion_matrix_file = os.path.join(
    interpretation_folder,
    "threshold_confusion_matrix.png"
)

plt.savefig(confusion_matrix_file)
plt.show()

print("Saved:", confusion_matrix_file)
print("Confusion matrix:")
print(threshold_matrix)
```

Expected result:

```text
A confusion matrix chart should display and be saved.
```

---

## Task 16 - Interpret the Confusion Matrix

The confusion matrix shows four outcomes:

```text
True Negative  = Actual legitimate, predicted legitimate
False Positive = Actual legitimate, predicted phishing
False Negative = Actual phishing, predicted legitimate
True Positive  = Actual phishing, predicted phishing
```

In cybersecurity:

```text
False Positive = A safe website is flagged as phishing.
False Negative = A phishing website is missed and treated as safe.
```

False negatives are usually very risky in phishing detection.

---

## Task 17 - Create a Model-Specific Feature Importance Table

Different models provide feature importance in different ways.

For Random Forest, this lab uses:

```text
feature_importances_
```

For Logistic Regression, this lab uses:

```text
absolute coefficient values
```

Run this code:

```python
feature_names = X_train.columns.tolist()

if isinstance(selected_model, RandomForestClassifier):
    importance_values = selected_model.feature_importances_
    importance_method = "Random Forest feature_importances_"

elif isinstance(selected_model, LogisticRegression):
    importance_values = abs(selected_model.coef_[0])
    importance_method = "Logistic Regression absolute coefficients"

else:
    raise ValueError("Unsupported model type for model-specific feature importance.")

model_specific_importance_df = pd.DataFrame({
    "feature": feature_names,
    "importance": importance_values
}).sort_values(
    by="importance",
    ascending=False
)

print("Importance method:", importance_method)

model_specific_importance_df.head(10)
```

Expected result:

```text
A table of the top 10 features should display.
```

---

## Task 18 - Save the Model-Specific Feature Importance Table

Run this code:

```python
model_specific_importance_file = os.path.join(
    interpretation_folder,
    "model_specific_feature_importance.csv"
)

model_specific_importance_df.to_csv(model_specific_importance_file, index=False)

print("Saved:", model_specific_importance_file)
```

Expected result:

```text
Saved: interpretation\model_specific_feature_importance.csv
```

---

## Task 19 - Plot the Top Model-Specific Features

Run this code:

```python
top_n = 10

top_model_features = model_specific_importance_df.head(top_n).sort_values(
    by="importance",
    ascending=True
)

plt.figure(figsize=(10, 6))

plt.barh(
    top_model_features["feature"],
    top_model_features["importance"]
)

plt.title("Top Model-Specific Feature Importance")
plt.xlabel("Importance")
plt.ylabel("Feature")
plt.tight_layout()

model_specific_chart_file = os.path.join(
    interpretation_folder,
    "model_specific_feature_importance.png"
)

plt.savefig(model_specific_chart_file)
plt.show()

print("Saved:", model_specific_chart_file)
```

Expected result:

```text
A horizontal bar chart should display and be saved.
```

---

## Task 20 - Understand Permutation Importance

Model-specific importance is useful, but it depends on the type of model.

Permutation importance works differently.

Permutation importance measures how much model performance changes when one feature is randomly shuffled.

If shuffling a feature causes performance to drop, that feature is likely important.

---

## Task 21 - Calculate Permutation Importance

Run this code:

```python
permutation_result = permutation_importance(
    selected_model,
    X_test_for_model,
    y_test,
    scoring="f1",
    n_repeats=10,
    random_state=42,
    n_jobs=-1
)

permutation_importance_df = pd.DataFrame({
    "feature": feature_names,
    "importance_mean": permutation_result.importances_mean,
    "importance_std": permutation_result.importances_std
}).sort_values(
    by="importance_mean",
    ascending=False
)

permutation_importance_df.head(10)
```

Expected result:

```text
A permutation importance table should display.
```

> [!note]
> This step may take a few minutes.

---

## Task 22 - Save the Permutation Importance Table

Run this code:

```python
permutation_importance_file = os.path.join(
    interpretation_folder,
    "permutation_importance.csv"
)

permutation_importance_df.to_csv(permutation_importance_file, index=False)

print("Saved:", permutation_importance_file)
```

Expected result:

```text
Saved: interpretation\permutation_importance.csv
```

---

## Task 23 - Plot Top Permutation Importance Features

Run this code:

```python
top_permutation_features = permutation_importance_df.head(10).sort_values(
    by="importance_mean",
    ascending=True
)

plt.figure(figsize=(10, 6))

plt.barh(
    top_permutation_features["feature"],
    top_permutation_features["importance_mean"]
)

plt.title("Top Permutation Importance Features")
plt.xlabel("Mean F1-score Decrease")
plt.ylabel("Feature")
plt.tight_layout()

permutation_chart_file = os.path.join(
    interpretation_folder,
    "permutation_importance.png"
)

plt.savefig(permutation_chart_file)
plt.show()

print("Saved:", permutation_chart_file)
```

Expected result:

```text
A horizontal bar chart should display and be saved.
```

---

## Task 24 - Compare the Two Importance Methods

Run this code:

```python
top_model_specific = set(model_specific_importance_df.head(10)["feature"])
top_permutation = set(permutation_importance_df.head(10)["feature"])

common_top_features = sorted(list(top_model_specific.intersection(top_permutation)))

print("Features that appear in both top-10 lists:")
for feature in common_top_features:
    print("-", feature)

print("\nNumber of common top features:", len(common_top_features))
```

Expected result:

```text
A list of common important features should display.
```

> [!knowledge]
> If a feature appears as important in multiple methods, analysts may have more confidence that it matters to the model.

---

## Task 25 - Compare Top Features by Target Class

This step calculates the average value of the most important features for each target class.

Run this code:

```python
top_features_for_summary = model_specific_importance_df.head(10)["feature"].tolist()

combined_test_df = X_test.copy()
combined_test_df["actual_target"] = y_test.values

top_features_by_target = combined_test_df.groupby("actual_target")[top_features_for_summary].mean()

top_features_by_target
```

Expected result:

```text
A table should display average values of top features by target class.
```

---

## Task 26 - Save the Top Features by Target Table

Run this code:

```python
top_features_by_target_file = os.path.join(
    interpretation_folder,
    "top_features_by_target.csv"
)

top_features_by_target.to_csv(top_features_by_target_file)

print("Saved:", top_features_by_target_file)
```

Expected result:

```text
Saved: interpretation\top_features_by_target.csv
```

---

## Task 27 - Create an Error Analysis Table

Run this code:

```python
error_analysis_df = X_test.copy()

error_analysis_df["actual"] = y_test.values
error_analysis_df["predicted"] = threshold_predictions
error_analysis_df["phishing_probability"] = phishing_probabilities

def classify_prediction(row):
    if row["actual"] == 0 and row["predicted"] == 0:
        return "true_negative"
    elif row["actual"] == 0 and row["predicted"] == 1:
        return "false_positive"
    elif row["actual"] == 1 and row["predicted"] == 0:
        return "false_negative"
    elif row["actual"] == 1 and row["predicted"] == 1:
        return "true_positive"
    else:
        return "unknown"

error_analysis_df["prediction_type"] = error_analysis_df.apply(
    classify_prediction,
    axis=1
)

error_analysis_df["prediction_type"].value_counts()
```

Expected result:

```text
A count of prediction types should display.
```

---

## Task 28 - Save the Error Analysis Table

Run this code:

```python
error_analysis_file = os.path.join(
    interpretation_folder,
    "error_analysis.csv"
)

error_analysis_df.to_csv(error_analysis_file, index=False)

print("Saved:", error_analysis_file)
```

Expected result:

```text
Saved: interpretation\error_analysis.csv
```

---

## Task 29 - Review False Positive Examples

False positives are legitimate websites predicted as phishing.

Run this code:

```python
false_positive_examples = error_analysis_df[
    error_analysis_df["prediction_type"] == "false_positive"
].sort_values(
    by="phishing_probability",
    ascending=False
).head(10)

false_positive_examples
```

Expected result:

```text
A table of up to 10 false positive examples should display.
```

---

## Task 30 - Save False Positive Examples

Run this code:

```python
false_positive_file = os.path.join(
    interpretation_folder,
    "false_positive_examples.csv"
)

false_positive_examples.to_csv(false_positive_file, index=False)

print("Saved:", false_positive_file)
```

Expected result:

```text
Saved: interpretation\false_positive_examples.csv
```

---

## Task 31 - Review False Negative Examples

False negatives are phishing websites predicted as legitimate.

Run this code:

```python
false_negative_examples = error_analysis_df[
    error_analysis_df["prediction_type"] == "false_negative"
].sort_values(
    by="phishing_probability",
    ascending=True
).head(10)

false_negative_examples
```

Expected result:

```text
A table of up to 10 false negative examples should display.
```

> [!alert]
> In phishing detection, false negatives are especially important because the model missed websites that should have been flagged.

---

## Task 32 - Save False Negative Examples

Run this code:

```python
false_negative_file = os.path.join(
    interpretation_folder,
    "false_negative_examples.csv"
)

false_negative_examples.to_csv(false_negative_file, index=False)

print("Saved:", false_negative_file)
```

Expected result:

```text
Saved: interpretation\false_negative_examples.csv
```

---

## Task 33 - Create an Interpretation Summary

Run this code:

```python
top_model_feature = model_specific_importance_df.iloc[0]["feature"]
top_permutation_feature = permutation_importance_df.iloc[0]["feature"]

prediction_counts = error_analysis_df["prediction_type"].value_counts()

summary_file = os.path.join(
    interpretation_folder,
    "lab08_interpretation_summary.txt"
)

with open(summary_file, "w") as file:
    file.write("Lab 08 - Model Interpretation Summary\n")
    file.write("====================================\n\n")

    file.write("Selected model: " + str(selected_model_name) + "\n")
    file.write("Selected threshold: " + str(selected_threshold) + "\n")
    file.write("Model uses scaled data: " + str(selected_model_uses_scaled_data) + "\n\n")

    file.write("Importance method: " + str(importance_method) + "\n")
    file.write("Top model-specific feature: " + str(top_model_feature) + "\n")
    file.write("Top permutation importance feature: " + str(top_permutation_feature) + "\n\n")

    file.write("Prediction type counts:\n")
    file.write(str(prediction_counts) + "\n\n")

    file.write("Common top features across importance methods:\n")
    if len(common_top_features) > 0:
        for feature in common_top_features:
            file.write("- " + str(feature) + "\n")
    else:
        file.write("No common features in the top-10 lists.\n")

print("Saved:", summary_file)
```

Expected result:

```text
Saved: interpretation\lab08_interpretation_summary.txt
```

---

## Task 34 - Create Lab 08 Metadata

Run this code:

```python
metadata_file = os.path.join(
    interpretation_folder,
    "lab08_metadata.txt"
)

with open(metadata_file, "w") as file:
    file.write("Lab 08 - Interpret Model and Feature Importance\n")
    file.write("Rows in test set: " + str(X_test.shape[0]) + "\n")
    file.write("Feature columns: " + str(X_test.shape[1]) + "\n")
    file.write("Selected model: " + str(selected_model_name) + "\n")
    file.write("Selected threshold: " + str(selected_threshold) + "\n")
    file.write("Model uses scaled data: " + str(selected_model_uses_scaled_data) + "\n")
    file.write("Model-specific importance method: " + str(importance_method) + "\n")
    file.write("Permutation importance scoring: f1\n")
    file.write("Top model-specific feature: " + str(top_model_feature) + "\n")
    file.write("Top permutation feature: " + str(top_permutation_feature) + "\n")
    file.write("False positives reviewed: " + str(len(false_positive_examples)) + "\n")
    file.write("False negatives reviewed: " + str(len(false_negative_examples)) + "\n")

print("Saved:", metadata_file)
```

Expected result:

```text
Saved: interpretation\lab08_metadata.txt
```

---

## Task 35 - List Interpretation Output Files

Run this code:

```python
print("Files in interpretation folder:")

for file_name in os.listdir(interpretation_folder):
    print("-", file_name)
```

Expected result:

```text
The interpretation output files should display.
```

You should see files such as:

```text
model_specific_feature_importance.csv
model_specific_feature_importance.png
permutation_importance.csv
permutation_importance.png
top_features_by_target.csv
threshold_confusion_matrix.png
error_analysis.csv
false_positive_examples.csv
false_negative_examples.csv
lab08_interpretation_summary.txt
lab08_metadata.txt
```

---

## Task 36 - Validate Your Work

Run this final validation cell:

```python
required_interpretation_files = [
    "model_specific_feature_importance.csv",
    "model_specific_feature_importance.png",
    "permutation_importance.csv",
    "permutation_importance.png",
    "top_features_by_target.csv",
    "threshold_confusion_matrix.png",
    "error_analysis.csv",
    "false_positive_examples.csv",
    "false_negative_examples.csv",
    "lab08_interpretation_summary.txt",
    "lab08_metadata.txt"
]

for file_name in required_interpretation_files:
    file_path = os.path.join(interpretation_folder, file_name)
    assert os.path.exists(file_path), f"Missing file: {file_name}"

assert X_test.shape == (2211, 30), "X_test should have 2,211 rows and 30 columns."
assert len(y_test) == 2211, "y_test should have 2,211 values."
assert len(feature_names) == 30, "There should be 30 feature names."

assert len(model_specific_importance_df) == 30, "Model-specific importance should contain 30 features."
assert len(permutation_importance_df) == 30, "Permutation importance should contain 30 features."

assert selected_threshold >= 0.10, "Selected threshold should be at least 0.10."
assert selected_threshold <= 0.90, "Selected threshold should be at most 0.90."

assert "prediction_type" in error_analysis_df.columns, "Error analysis should include prediction_type."
assert "phishing_probability" in error_analysis_df.columns, "Error analysis should include phishing_probability."

print("Validation passed. Lab 08 is complete.")
```

Expected result:

```text
Validation passed. Lab 08 is complete.
```

---

## Task 37 - Save Your Notebook

1. Select **File**.
2. Select **Save and Checkpoint**.

Confirm the notebook is named:

```text
Lab08-Interpret-Model-Feature-Importance.ipynb
```

---

## Knowledge Check

Answer the following questions:

1. Why is model interpretation important in cybersecurity?
2. What is feature importance?
3. How does Random Forest feature importance differ from Logistic Regression coefficient importance?
4. What does permutation importance measure?
5. Why might a feature appear important in one method but not another?
6. What is a false positive in phishing detection?
7. What is a false negative in phishing detection?
8. Why are false negatives especially risky?
9. What did the top features suggest about the selected model?
10. How could an analyst use the error analysis table?

---

## Troubleshooting

### Problem: Tuning Folder Is Missing

Run:

```python
import os

print(os.getcwd())
print(os.listdir())
```

Check whether the current folder contains:

```text
tuning
```

If not, check whether Lab 06 contains:

```text
Lab06/tuning
```

If the tuning files are missing, return to Lab 06 and complete the validation step.

---

### Problem: Processed Folder Is Missing

Check whether the current folder contains:

```text
processed
```

If not, check whether one of these folders exists:

```text
../Lab06/processed
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

### Problem: Permutation Importance Takes Too Long

Reduce the number of repeats.

Change this:

```python
n_repeats=10
```

to:

```python
n_repeats=5
```

Then rerun the permutation importance task.

---

### Problem: Feature Importance Looks Different from Permutation Importance

This can happen.

Model-specific importance and permutation importance measure different things.

Use both as interpretation signals, not absolute proof.

---

### Problem: Validation Fails Because Shape Is Different

Run:

```python
print(X_test.shape)
print(y_test.shape)
```

Expected values are:

```text
X_test: (2211, 30)
y_test: (2211,)
```

If these values are different, return to Lab 03 and recreate the processed files using the original dataset.

---

## Summary

In this lab, you interpreted the tuned cybersecurity classification model.

You completed the following tasks:

- Loaded the selected tuned model from Lab 06.
- Loaded the selected threshold from Lab 06.
- Loaded processed test data.
- Generated threshold-based predictions.
- Created a confusion matrix.
- Calculated model-specific feature importance.
- Calculated permutation importance.
- Compared the two feature importance methods.
- Compared top features by target class.
- Created an error analysis table.
- Reviewed false positive examples.
- Reviewed false negative examples.
- Saved interpretation reports and charts.
- Validated all required Lab 08 outputs.

In the next lab, you should cover unsupervised learning. A strong next topic is:

```text
Lab 09 - Unsupervised Learning for Cybersecurity Anomaly Detection
```

That lab can use clustering and anomaly detection to find suspicious website records without using the target label.
