# Lab 04 - Train a Cybersecurity Classification Model

## Estimated Time

60 minutes

## Learning Objectives

By the end of this lab, you will be able to:

- Continue from the processed dataset created in Lab 03.
- Load training and testing files into pandas.
- Train a baseline cybersecurity classification model.
- Train a Logistic Regression model.
- Train a Decision Tree model.
- Train a Random Forest model.
- Compare basic model accuracy.
- Save trained models as `.joblib` files.
- Save model predictions for use in the next lab.
- Validate that the models were trained and saved successfully.

---

## Scenario

You are working as a junior cybersecurity data analyst.

In previous labs, you completed the following work:

- **Lab 01:** Loaded the phishing websites dataset.
- **Lab 02:** Explored and visualized the dataset.
- **Lab 03:** Prepared the dataset for machine learning.

In this lab, you will train machine learning models that can classify websites based on their features.

The goal is to create models that can learn patterns from the training data and make predictions on the testing data.

---

## What You Will Create

In this lab, you will create the following files:

```text
models/baseline_model.joblib
models/logistic_regression_model.joblib
models/decision_tree_model.joblib
models/random_forest_model.joblib
models/lab04_model_summary.csv
models/logistic_regression_predictions.csv
models/decision_tree_predictions.csv
models/random_forest_predictions.csv
models/lab04_metadata.txt
```

These files will be used in Lab 05 for deeper model evaluation.

---

## Before You Begin

You should have completed Lab 03.

Lab 03 created a folder named:

```text
processed
```

The `processed` folder should contain:

```text
X_train.csv
X_test.csv
y_train.csv
y_test.csv
X_train_scaled.csv
X_test_scaled.csv
scaler.joblib
lab03_metadata.txt
```

> [!note]
> If these files are not in your Lab04 folder, this lab includes a step to copy them from the Lab03 folder.

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

## Task 2 - Create a Lab 04 Folder

In the Jupyter Notebook file browser:

1. Select **New**.
2. Select **Folder**.
3. Select the checkbox beside the new folder.
4. Select **Rename**.
5. Rename the folder to:

```text
Lab04
```

6. Open the **Lab04** folder.

---

## Task 3 - Create a New Python Notebook

1. Inside the **Lab04** folder, select **New**.
2. Select **Python 3 (ipykernel)**.
3. Rename the notebook to:

```text
Lab04-Train-Classification-Model.ipynb
```

To rename the notebook:

1. Select the notebook title at the top of the page.
2. Enter the new name.
3. Select **Rename**.

---

## Task 4 - Install Required Packages

In the first notebook cell, run:

```python
%pip install pandas scikit-learn joblib
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
import joblib

from sklearn.dummy import DummyClassifier
from sklearn.linear_model import LogisticRegression
from sklearn.tree import DecisionTreeClassifier
from sklearn.ensemble import RandomForestClassifier

from sklearn.metrics import accuracy_score
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
The current Lab04 folder path should display.
```

---

## Task 7 - Locate or Copy the Processed Files from Lab 03

Run this code:

```python
processed_folder = "processed"

required_processed_files = [
    "X_train.csv",
    "X_test.csv",
    "y_train.csv",
    "y_test.csv",
    "X_train_scaled.csv",
    "X_test_scaled.csv",
    "scaler.joblib",
    "lab03_metadata.txt"
]

def processed_files_exist(folder):
    return os.path.isdir(folder) and all(
        os.path.exists(os.path.join(folder, file_name))
        for file_name in required_processed_files
    )

if processed_files_exist(processed_folder):
    print("Processed folder found in the current Lab04 folder.")

else:
    possible_lab03_processed_folder = os.path.join("..", "Lab03", "processed")

    if processed_files_exist(possible_lab03_processed_folder):
        print("Found processed folder in ../Lab03/processed")
        print("Copying processed files into the Lab04 folder...")

        shutil.copytree(
            possible_lab03_processed_folder,
            processed_folder,
            dirs_exist_ok=True
        )

        print("Processed files copied successfully.")

    else:
        print("Processed files were not found.")
        print("Expected to find them in one of these locations:")
        print("- processed")
        print("- ../Lab03/processed")
```

Expected result:

```text
Processed files copied successfully.
```

or:

```text
Processed folder found in the current Lab04 folder.
```

> [!alert]
> If the processed files are not found, return to Lab 03 and complete the final validation step. Then come back to Lab 04.

---

## Task 8 - Verify the Processed Files

Run this code:

```python
print("Files in processed folder:")

for file_name in os.listdir(processed_folder):
    print("-", file_name)
```

You should see:

```text
X_train.csv
X_test.csv
y_train.csv
y_test.csv
X_train_scaled.csv
X_test_scaled.csv
scaler.joblib
lab03_metadata.txt
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

The target values should be:

```text
0
1
```

In this lab:

```text
1 = phishing or suspicious
0 = legitimate or benign
```

---

## Task 11 - Create a Models Folder

Run this code:

```python
models_folder = "models"

os.makedirs(models_folder, exist_ok=True)

print("Models folder created:", models_folder)
```

Expected result:

```text
Models folder created: models
```

---

## Task 12 - Train a Baseline Model

A baseline model gives you a simple comparison point.

This model does not learn complex cybersecurity patterns. It simply predicts the most common class.

Run this code:

```python
baseline_model = DummyClassifier(strategy="most_frequent")

baseline_model.fit(X_train, y_train)

baseline_predictions = baseline_model.predict(X_test)

baseline_accuracy = accuracy_score(y_test, baseline_predictions)

print("Baseline model accuracy:", round(baseline_accuracy, 4))
```

Expected result:

```text
A baseline accuracy score should display.
```

> [!knowledge]
> A baseline model helps answer this question: “Is my real model better than a very simple guess?”

---

## Task 13 - Save the Baseline Model

Run this code:

```python
baseline_model_file = os.path.join(models_folder, "baseline_model.joblib")

joblib.dump(baseline_model, baseline_model_file)

print("Saved:", baseline_model_file)
```

Expected result:

```text
Saved: models\baseline_model.joblib
```

---

## Task 14 - Train a Logistic Regression Model

Logistic Regression is a common classification algorithm.

It is often used as a first machine learning model because it is simple, fast, and easy to understand.

For Logistic Regression, use the scaled data:

```text
X_train_scaled
X_test_scaled
```

Run this code:

```python
logistic_regression_model = LogisticRegression(
    max_iter=1000,
    random_state=42
)

logistic_regression_model.fit(X_train_scaled, y_train)

logistic_regression_predictions = logistic_regression_model.predict(X_test_scaled)

logistic_regression_accuracy = accuracy_score(
    y_test,
    logistic_regression_predictions
)

print("Logistic Regression accuracy:", round(logistic_regression_accuracy, 4))
```

Expected result:

```text
A Logistic Regression accuracy score should display.
```

---

## Task 15 - Save the Logistic Regression Model

Run this code:

```python
logistic_model_file = os.path.join(
    models_folder,
    "logistic_regression_model.joblib"
)

joblib.dump(logistic_regression_model, logistic_model_file)

print("Saved:", logistic_model_file)
```

Expected result:

```text
Saved: models\logistic_regression_model.joblib
```

---

## Task 16 - Save Logistic Regression Predictions

Run this code:

```python
logistic_predictions_df = pd.DataFrame({
    "actual": y_test,
    "predicted": logistic_regression_predictions
})

logistic_predictions_file = os.path.join(
    models_folder,
    "logistic_regression_predictions.csv"
)

logistic_predictions_df.to_csv(logistic_predictions_file, index=False)

print("Saved:", logistic_predictions_file)
```

Expected result:

```text
Saved: models\logistic_regression_predictions.csv
```

---

## Task 17 - Train a Decision Tree Model

A Decision Tree model learns decision rules from the data.

Decision Trees are useful because they are easier to explain than many other models.

Run this code:

```python
decision_tree_model = DecisionTreeClassifier(
    random_state=42,
    max_depth=5
)

decision_tree_model.fit(X_train, y_train)

decision_tree_predictions = decision_tree_model.predict(X_test)

decision_tree_accuracy = accuracy_score(y_test, decision_tree_predictions)

print("Decision Tree accuracy:", round(decision_tree_accuracy, 4))
```

Expected result:

```text
A Decision Tree accuracy score should display.
```

> [!note]
> This model uses the unscaled data. Decision Trees do not require feature scaling in the same way that Logistic Regression often does.

---

## Task 18 - Save the Decision Tree Model

Run this code:

```python
decision_tree_model_file = os.path.join(
    models_folder,
    "decision_tree_model.joblib"
)

joblib.dump(decision_tree_model, decision_tree_model_file)

print("Saved:", decision_tree_model_file)
```

Expected result:

```text
Saved: models\decision_tree_model.joblib
```

---

## Task 19 - Save Decision Tree Predictions

Run this code:

```python
decision_tree_predictions_df = pd.DataFrame({
    "actual": y_test,
    "predicted": decision_tree_predictions
})

decision_tree_predictions_file = os.path.join(
    models_folder,
    "decision_tree_predictions.csv"
)

decision_tree_predictions_df.to_csv(decision_tree_predictions_file, index=False)

print("Saved:", decision_tree_predictions_file)
```

Expected result:

```text
Saved: models\decision_tree_predictions.csv
```

---

## Task 20 - Train a Random Forest Model

A Random Forest model combines multiple Decision Trees.

Random Forest models often perform better than a single Decision Tree because they combine results from many trees.

Run this code:

```python
random_forest_model = RandomForestClassifier(
    n_estimators=100,
    random_state=42,
    max_depth=None
)

random_forest_model.fit(X_train, y_train)

random_forest_predictions = random_forest_model.predict(X_test)

random_forest_accuracy = accuracy_score(y_test, random_forest_predictions)

print("Random Forest accuracy:", round(random_forest_accuracy, 4))
```

Expected result:

```text
A Random Forest accuracy score should display.
```

---

## Task 21 - Save the Random Forest Model

Run this code:

```python
random_forest_model_file = os.path.join(
    models_folder,
    "random_forest_model.joblib"
)

joblib.dump(random_forest_model, random_forest_model_file)

print("Saved:", random_forest_model_file)
```

Expected result:

```text
Saved: models\random_forest_model.joblib
```

---

## Task 22 - Save Random Forest Predictions

Run this code:

```python
random_forest_predictions_df = pd.DataFrame({
    "actual": y_test,
    "predicted": random_forest_predictions
})

random_forest_predictions_file = os.path.join(
    models_folder,
    "random_forest_predictions.csv"
)

random_forest_predictions_df.to_csv(random_forest_predictions_file, index=False)

print("Saved:", random_forest_predictions_file)
```

Expected result:

```text
Saved: models\random_forest_predictions.csv
```

---

## Task 23 - Compare Model Accuracy

Run this code:

```python
model_summary = pd.DataFrame({
    "model": [
        "Baseline",
        "Logistic Regression",
        "Decision Tree",
        "Random Forest"
    ],
    "accuracy": [
        baseline_accuracy,
        logistic_regression_accuracy,
        decision_tree_accuracy,
        random_forest_accuracy
    ]
})

model_summary["accuracy"] = model_summary["accuracy"].round(4)

model_summary
```

Expected result:

```text
A table should display the accuracy for each model.
```

---

## Task 24 - Save the Model Summary

Run this code:

```python
model_summary_file = os.path.join(models_folder, "lab04_model_summary.csv")

model_summary.to_csv(model_summary_file, index=False)

print("Saved:", model_summary_file)
```

Expected result:

```text
Saved: models\lab04_model_summary.csv
```

---

## Task 25 - Identify the Best Model by Accuracy

Run this code:

```python
best_model_row = model_summary.sort_values(
    by="accuracy",
    ascending=False
).iloc[0]

best_model_name = best_model_row["model"]
best_model_accuracy = best_model_row["accuracy"]

print("Best model by accuracy:", best_model_name)
print("Best accuracy:", best_model_accuracy)
```

Expected result:

```text
The model with the highest accuracy should display.
```

> [!alert]
> Accuracy is useful, but it is not the only metric. In cybersecurity, false negatives can be especially risky. In Lab 05, you will evaluate models more carefully using confusion matrices, precision, recall, and F1-score.

---

## Task 26 - Save Lab 04 Metadata

Run this code:

```python
metadata_file = os.path.join(models_folder, "lab04_metadata.txt")

with open(metadata_file, "w") as file:
    file.write("Lab 04 - Train a Cybersecurity Classification Model\n")
    file.write("Training rows: " + str(X_train.shape[0]) + "\n")
    file.write("Testing rows: " + str(X_test.shape[0]) + "\n")
    file.write("Feature columns: " + str(X_train.shape[1]) + "\n")
    file.write("Models trained: Baseline, Logistic Regression, Decision Tree, Random Forest\n")
    file.write("Baseline accuracy: " + str(round(baseline_accuracy, 4)) + "\n")
    file.write("Logistic Regression accuracy: " + str(round(logistic_regression_accuracy, 4)) + "\n")
    file.write("Decision Tree accuracy: " + str(round(decision_tree_accuracy, 4)) + "\n")
    file.write("Random Forest accuracy: " + str(round(random_forest_accuracy, 4)) + "\n")
    file.write("Best model by accuracy: " + str(best_model_name) + "\n")
    file.write("Best accuracy: " + str(best_model_accuracy) + "\n")

print("Saved:", metadata_file)
```

Expected result:

```text
Saved: models\lab04_metadata.txt
```

---

## Task 27 - List the Model Files

Run this code:

```python
print("Files in models folder:")

for file_name in os.listdir(models_folder):
    print("-", file_name)
```

You should see files similar to:

```text
baseline_model.joblib
logistic_regression_model.joblib
decision_tree_model.joblib
random_forest_model.joblib
logistic_regression_predictions.csv
decision_tree_predictions.csv
random_forest_predictions.csv
lab04_model_summary.csv
lab04_metadata.txt
```

---

## Task 28 - Reload a Saved Model

To confirm that saved models work, reload the Random Forest model.

Run this code:

```python
loaded_random_forest_model = joblib.load(random_forest_model_file)

loaded_predictions = loaded_random_forest_model.predict(X_test)

loaded_accuracy = accuracy_score(y_test, loaded_predictions)

print("Reloaded Random Forest accuracy:", round(loaded_accuracy, 4))
print("Original Random Forest accuracy:", round(random_forest_accuracy, 4))
```

Expected result:

```text
The reloaded model accuracy should match the original Random Forest accuracy.
```

---

## Task 29 - Validate Your Work

Run this final validation cell:

```python
required_model_files = [
    "baseline_model.joblib",
    "logistic_regression_model.joblib",
    "decision_tree_model.joblib",
    "random_forest_model.joblib",
    "logistic_regression_predictions.csv",
    "decision_tree_predictions.csv",
    "random_forest_predictions.csv",
    "lab04_model_summary.csv",
    "lab04_metadata.txt"
]

for file_name in required_model_files:
    file_path = os.path.join(models_folder, file_name)
    assert os.path.exists(file_path), f"Missing file: {file_name}"

assert X_train.shape == (8844, 30), "X_train should have 8,844 rows and 30 columns."
assert X_test.shape == (2211, 30), "X_test should have 2,211 rows and 30 columns."

assert len(y_train) == 8844, "y_train should have 8,844 values."
assert len(y_test) == 2211, "y_test should have 2,211 values."

assert baseline_accuracy >= 0, "Baseline accuracy should be valid."
assert logistic_regression_accuracy >= 0, "Logistic Regression accuracy should be valid."
assert decision_tree_accuracy >= 0, "Decision Tree accuracy should be valid."
assert random_forest_accuracy >= 0, "Random Forest accuracy should be valid."

assert logistic_regression_accuracy <= 1, "Logistic Regression accuracy should be less than or equal to 1."
assert decision_tree_accuracy <= 1, "Decision Tree accuracy should be less than or equal to 1."
assert random_forest_accuracy <= 1, "Random Forest accuracy should be less than or equal to 1."

print("Validation passed. Lab 04 is complete.")
```

Expected result:

```text
Validation passed. Lab 04 is complete.
```

---

## Task 30 - Save Your Notebook

1. Select **File**.
2. Select **Save and Checkpoint**.

Confirm the notebook is named:

```text
Lab04-Train-Classification-Model.ipynb
```

---

## Knowledge Check

Answer the following questions:

1. What is the purpose of a baseline model?
2. Why did Logistic Regression use scaled data?
3. Why can Decision Trees and Random Forests use unscaled data?
4. What does model accuracy measure?
5. Which model had the highest accuracy in your lab?
6. Why is accuracy not enough for cybersecurity model evaluation?
7. What files did you save for use in Lab 05?
8. What is the purpose of saving trained models with `joblib`?
9. What is the difference between training data and testing data?
10. Why should a model be tested on data it did not train on?

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

If not, check whether Lab 03 has this folder:

```text
Lab03/processed
```

Complete Lab 03 again if needed.

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

### Problem: Shape Validation Fails

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

If the shapes are different, return to Lab 03 and recreate the processed files.

---

### Problem: Logistic Regression Does Not Converge

If you see a convergence warning, increase the maximum iterations:

```python
logistic_regression_model = LogisticRegression(
    max_iter=2000,
    random_state=42
)
```

Then rerun the Logistic Regression training cell.

---

### Problem: Model File Was Not Created

Run:

```python
print(os.listdir("models"))
```

If a model file is missing, rerun the save cell for that model.

---

## Summary

In this lab, you trained your first cybersecurity classification models.

You completed the following tasks:

- Loaded the processed dataset from Lab 03.
- Verified the training and testing files.
- Created a models folder.
- Trained a baseline model.
- Trained a Logistic Regression model.
- Trained a Decision Tree model.
- Trained a Random Forest model.
- Compared model accuracy.
- Identified the best model by accuracy.
- Saved trained models.
- Saved prediction files.
- Saved a model summary file.
- Reloaded a saved model to confirm it works.
- Validated all required Lab 04 outputs.

In Lab 05, you will evaluate the trained models in more detail using confusion matrices, precision, recall, F1-score, and cybersecurity-focused interpretation.
