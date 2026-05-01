# Lab 03 - Prepare the Cybersecurity Dataset for Machine Learning

## Estimated Time

60 minutes

## Learning Objectives

By the end of this lab, you will be able to:

- Continue working with the phishing websites dataset from Labs 01 and 02.
- Load the explored cybersecurity dataset into pandas.
- Confirm the dataset structure before machine learning.
- Identify the feature columns and target column.
- Convert the target column into a machine-learning-friendly format.
- Separate the dataset into features and labels.
- Split the dataset into training and testing sets.
- Use stratified splitting to preserve class balance.
- Scale feature values using `StandardScaler`.
- Save processed training and testing files for later labs.
- Validate that the machine learning preparation process was completed successfully.

---

## Scenario

You are working as a junior cybersecurity data analyst.

In Lab 01, you loaded the UCI Phishing Websites dataset.

In Lab 02, you explored and visualized the dataset.

In this lab, you will prepare the dataset so that it can be used to train a machine learning model in the next lab.

Before a model can be trained, the data must be prepared correctly. This usually includes checking the data, separating input features from the target label, encoding the target column, splitting the dataset into training and testing sets, and saving the processed files.

---

## What You Will Create

In this lab, you will create the following files:

```text
phishing_websites_ml_ready.csv
X_train.csv
X_test.csv
y_train.csv
y_test.csv
X_train_scaled.csv
X_test_scaled.csv
scaler.joblib
lab03_metadata.txt
```

These files will be used in later labs.

---

## Before You Begin

You should have completed Lab 01 and Lab 02.

You should have one of the following files:

```text
phishing_websites_explored.csv
```

or:

```text
phishing_websites.csv
```

The preferred file for this lab is:

```text
phishing_websites_explored.csv
```

This file was created in Lab 02.

> [!note]
> If the explored file is missing, this lab will fall back to `phishing_websites.csv`. If both files are missing, the lab includes a recovery step to download the dataset again.

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

## Task 2 - Create a Lab 03 Folder

In the Jupyter Notebook file browser:

1. Select **New**.
2. Select **Folder**.
3. Select the checkbox beside the new folder.
4. Select **Rename**.
5. Rename the folder to:

```text
Lab03
```

6. Open the **Lab03** folder.

---

## Task 3 - Create a New Python Notebook

1. Inside the **Lab03** folder, select **New**.
2. Select **Python 3 (ipykernel)**.
3. Rename the notebook to:

```text
Lab03-Prepare-Dataset-for-ML.ipynb
```

To rename the notebook:

1. Select the notebook title at the top of the page.
2. Enter the new name.
3. Select **Rename**.

---

## Task 4 - Install Required Packages

In the first notebook cell, run:

```python
%pip install pandas scikit-learn joblib ucimlrepo
```

Expected result:

```text
The packages install successfully or show "Requirement already satisfied".
```

> [!note]
> `pandas` is used for data handling.  
> `scikit-learn` is used for train/test splitting and scaling.  
> `joblib` is used to save the scaler.  
> `ucimlrepo` is used only as a recovery method if the dataset files are missing.

---

## Task 5 - Import Python Libraries

In a new notebook cell, run:

```python
import os
import pandas as pd
import joblib

from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler
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

print("\nFiles in this folder:")
print(os.listdir())
```

Review the output.

You are looking for one of these files:

```text
phishing_websites_explored.csv
phishing_websites.csv
```

If the files are not in the current folder, you can either:

- Move the file from Lab 01 or Lab 02 into the Lab03 folder.
- Use the recovery step included in the next task.

---

## Task 7 - Load the Dataset

Run this code:

```python
preferred_file = "phishing_websites_explored.csv"
backup_file = "phishing_websites.csv"

if os.path.exists(preferred_file):
    df = pd.read_csv(preferred_file)
    source_file = preferred_file
    print(f"Loaded dataset from {preferred_file}")

elif os.path.exists(backup_file):
    df = pd.read_csv(backup_file)
    source_file = backup_file
    print(f"Loaded dataset from {backup_file}")

else:
    print("Local dataset files were not found.")
    print("Downloading the dataset again from the UCI Machine Learning Repository...")

    from ucimlrepo import fetch_ucirepo

    phishing_websites = fetch_ucirepo(id=327)

    X = phishing_websites.data.features
    y = phishing_websites.data.targets

    df = pd.concat([X, y], axis=1)
    source_file = "downloaded_from_uci"

    df.to_csv(backup_file, index=False)

    print("Dataset downloaded and saved as phishing_websites.csv")

print("\nDataset source:", source_file)
print("Rows and columns:", df.shape)

df.head()
```

Expected result:

```text
Rows and columns: (11055, 31)
```

---

## Task 8 - Confirm Dataset Shape

Run this code:

```python
print("Number of rows:", df.shape[0])
print("Number of columns:", df.shape[1])
```

Expected result:

```text
Number of rows: 11055
Number of columns: 31
```

The dataset should contain:

- 11,055 website records
- 30 feature columns
- 1 target column

---

## Task 9 - Review the Column Names

Run this code:

```python
for index, column in enumerate(df.columns, start=1):
    print(index, column)
```

Expected result:

```text
A numbered list of all dataset columns should display.
```

The final column will be treated as the target column.

---

## Task 10 - Identify the Target Column

In machine learning, the **target column** is the value the model will try to predict.

For this dataset, the target column is expected to be the last column.

Run this code:

```python
target_column = df.columns[-1]

print("Target column selected:")
print(target_column)
```

Expected result:

```text
The selected target column name should display.
```

---

## Task 11 - View Target Values

Run this code:

```python
print("Target value counts:")
print(df[target_column].value_counts())

print("\nUnique target values:")
print(sorted(df[target_column].unique()))
```

Expected result:

```text
A list of target values and their counts should display.
```

The dataset commonly uses values such as:

```text
-1
1
```

These values represent different website classes.

---

## Task 12 - Create a Clean Working Copy

Before changing the target labels, create a working copy of the dataset.

Run this code:

```python
df_ml = df.copy()

print("Created machine learning working copy.")
print("Rows and columns:", df_ml.shape)
```

Expected result:

```text
Created machine learning working copy.
Rows and columns: (11055, 31)
```

---

## Task 13 - Encode the Target Column

Machine learning models usually work best when the target column has clear numeric labels.

In this lab, you will create a new target column called:

```text
target_binary
```

For the common UCI phishing dataset format:

```text
-1 = phishing or suspicious
 1 = legitimate or benign
```

This lab will encode the target as:

```text
1 = phishing or suspicious
0 = legitimate or benign
```

Run this code:

```python
original_target_values = set(df_ml[target_column].unique())

print("Original target values:", original_target_values)

if original_target_values == {-1, 1}:
    df_ml["target_binary"] = df_ml[target_column].map({
        -1: 1,
         1: 0
    })

    print("Target encoded using phishing-focused mapping:")
    print("-1 converted to 1")
    print(" 1 converted to 0")

else:
    print("Unexpected target values detected.")
    print("Using automatic category encoding.")

    df_ml["target_binary"] = df_ml[target_column].astype("category").cat.codes

print("\nEncoded target counts:")
print(df_ml["target_binary"].value_counts())
```

Expected result:

```text
The encoded target counts should display.
```

> [!knowledge]
> Encoding the target column makes it easier to train a classification model. In this lab, `1` means the record belongs to the cybersecurity risk class, and `0` means the record belongs to the other class.

---

## Task 14 - Remove the Original Target Column from the Feature Set

The model should use website features as inputs.

The model should not use the original target column as an input feature.

Run this code:

```python
feature_columns = [column for column in df_ml.columns if column not in [target_column, "target_binary"]]

print("Number of feature columns:", len(feature_columns))

print("\nFeature columns:")
for column in feature_columns:
    print("-", column)
```

Expected result:

```text
Number of feature columns: 30
```

---

## Task 15 - Separate Features and Labels

In machine learning:

- `X` usually means the input features.
- `y` usually means the target label.

Run this code:

```python
X = df_ml[feature_columns]
y = df_ml["target_binary"]

print("Feature matrix shape:", X.shape)
print("Target vector shape:", y.shape)
```

Expected result:

```text
Feature matrix shape: (11055, 30)
Target vector shape: (11055,)
```

---

## Task 16 - Confirm All Feature Columns Are Numeric

Run this code:

```python
non_numeric_columns = X.select_dtypes(exclude=["number"]).columns.tolist()

print("Non-numeric feature columns:")
print(non_numeric_columns)
```

Expected result:

```text
Non-numeric feature columns:
[]
```

If the list is empty, all feature columns are numeric.

---

## Task 17 - Check for Missing Values Again

Run this code:

```python
missing_features = X.isnull().sum().sum()
missing_target = y.isnull().sum()

print("Missing values in features:", missing_features)
print("Missing values in target:", missing_target)
```

Expected result:

```text
Missing values in features: 0
Missing values in target: 0
```

---

## Task 18 - Check Class Balance Before Splitting

Run this code:

```python
class_counts = y.value_counts()
class_percentages = y.value_counts(normalize=True) * 100

print("Class counts:")
print(class_counts)

print("\nClass percentages:")
print(class_percentages.round(2))
```

Expected result:

```text
Counts and percentages for each target class should display.
```

> [!note]
> Checking class balance is important. If one class is much larger than the other, a model may learn to favor the majority class.

---

## Task 19 - Split the Dataset into Training and Testing Sets

The training set is used to train a model.

The testing set is used to evaluate how well the model works on unseen data.

This lab uses:

```text
80% training data
20% testing data
```

Run this code:

```python
X_train, X_test, y_train, y_test = train_test_split(
    X,
    y,
    test_size=0.20,
    random_state=42,
    stratify=y
)

print("X_train shape:", X_train.shape)
print("X_test shape:", X_test.shape)
print("y_train shape:", y_train.shape)
print("y_test shape:", y_test.shape)
```

Expected result:

```text
X_train shape: (8844, 30)
X_test shape: (2211, 30)
y_train shape: (8844,)
y_test shape: (2211,)
```

> [!knowledge]
> `stratify=y` keeps the class distribution similar in the training and testing sets.

---

## Task 20 - Compare Class Balance After Splitting

Run this code:

```python
print("Training target distribution:")
print(y_train.value_counts(normalize=True).round(4))

print("\nTesting target distribution:")
print(y_test.value_counts(normalize=True).round(4))
```

Expected result:

```text
The training and testing class distributions should be similar.
```

This confirms that the stratified split worked correctly.

---

## Task 21 - Scale the Feature Values

Some machine learning algorithms work better when feature values are scaled.

In this lab, you will use `StandardScaler`.

`StandardScaler` transforms feature values so they have:

```text
mean close to 0
standard deviation close to 1
```

Run this code:

```python
scaler = StandardScaler()

X_train_scaled_array = scaler.fit_transform(X_train)
X_test_scaled_array = scaler.transform(X_test)

X_train_scaled = pd.DataFrame(
    X_train_scaled_array,
    columns=X_train.columns,
    index=X_train.index
)

X_test_scaled = pd.DataFrame(
    X_test_scaled_array,
    columns=X_test.columns,
    index=X_test.index
)

print("Scaled training data shape:", X_train_scaled.shape)
print("Scaled testing data shape:", X_test_scaled.shape)
```

Expected result:

```text
Scaled training data shape: (8844, 30)
Scaled testing data shape: (2211, 30)
```

> [!alert]
> Notice that `fit_transform()` is used only on the training data.  
> The testing data uses `transform()` only.  
> This helps avoid data leakage.

---

## Task 22 - Understand Data Leakage

Data leakage happens when information from the test set is accidentally used during training.

This can make a model look better than it really is.

In this lab, you avoided data leakage by fitting the scaler only on the training data:

```python
scaler.fit_transform(X_train)
```

Then you applied the already-fitted scaler to the test data:

```python
scaler.transform(X_test)
```

This is the correct approach.

---

## Task 23 - Create a Processed Output Folder

Run this code:

```python
output_folder = "processed"

os.makedirs(output_folder, exist_ok=True)

print("Output folder created:", output_folder)
```

Expected result:

```text
Output folder created: processed
```

---

## Task 24 - Save the Machine Learning Ready Dataset

Run this code:

```python
ml_ready_file = os.path.join(output_folder, "phishing_websites_ml_ready.csv")

df_ml.to_csv(ml_ready_file, index=False)

print("Saved:", ml_ready_file)
```

Expected result:

```text
Saved: processed\phishing_websites_ml_ready.csv
```

This file contains the original feature columns plus the new `target_binary` column.

---

## Task 25 - Save the Training and Testing Files

Run this code:

```python
X_train.to_csv(os.path.join(output_folder, "X_train.csv"), index=False)
X_test.to_csv(os.path.join(output_folder, "X_test.csv"), index=False)

y_train.to_csv(os.path.join(output_folder, "y_train.csv"), index=False)
y_test.to_csv(os.path.join(output_folder, "y_test.csv"), index=False)

print("Saved unscaled training and testing files.")
```

Expected result:

```text
Saved unscaled training and testing files.
```

---

## Task 26 - Save the Scaled Training and Testing Files

Run this code:

```python
X_train_scaled.to_csv(os.path.join(output_folder, "X_train_scaled.csv"), index=False)
X_test_scaled.to_csv(os.path.join(output_folder, "X_test_scaled.csv"), index=False)

print("Saved scaled training and testing files.")
```

Expected result:

```text
Saved scaled training and testing files.
```

---

## Task 27 - Save the Scaler

Run this code:

```python
scaler_file = os.path.join(output_folder, "scaler.joblib")

joblib.dump(scaler, scaler_file)

print("Saved scaler:", scaler_file)
```

Expected result:

```text
Saved scaler: processed\scaler.joblib
```

The scaler can be reused later when preparing new data for the model.

---

## Task 28 - Create a Metadata File

A metadata file helps you remember how the data was prepared.

Run this code:

```python
metadata_file = os.path.join(output_folder, "lab03_metadata.txt")

with open(metadata_file, "w") as file:
    file.write("Lab 03 - Prepare Dataset for Machine Learning\n")
    file.write("Source file: " + str(source_file) + "\n")
    file.write("Rows: " + str(df_ml.shape[0]) + "\n")
    file.write("Original columns: " + str(df.shape[1]) + "\n")
    file.write("Feature columns: " + str(len(feature_columns)) + "\n")
    file.write("Target column: " + str(target_column) + "\n")
    file.write("Encoded target column: target_binary\n")
    file.write("Train rows: " + str(X_train.shape[0]) + "\n")
    file.write("Test rows: " + str(X_test.shape[0]) + "\n")
    file.write("Test size: 20%\n")
    file.write("Random state: 42\n")
    file.write("Stratified split: True\n")
    file.write("Scaling method: StandardScaler\n")

print("Saved metadata file:", metadata_file)
```

Expected result:

```text
Saved metadata file: processed\lab03_metadata.txt
```

---

## Task 29 - List the Processed Files

Run this code:

```python
print("Files in processed folder:")

for file_name in os.listdir(output_folder):
    print("-", file_name)
```

Expected result:

```text
The processed folder should contain the files created in this lab.
```

You should see:

```text
phishing_websites_ml_ready.csv
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

## Task 30 - Reload Saved Files to Confirm They Work

Run this code:

```python
reloaded_X_train = pd.read_csv(os.path.join(output_folder, "X_train.csv"))
reloaded_X_test = pd.read_csv(os.path.join(output_folder, "X_test.csv"))
reloaded_y_train = pd.read_csv(os.path.join(output_folder, "y_train.csv"))
reloaded_y_test = pd.read_csv(os.path.join(output_folder, "y_test.csv"))

print("Reloaded X_train shape:", reloaded_X_train.shape)
print("Reloaded X_test shape:", reloaded_X_test.shape)
print("Reloaded y_train shape:", reloaded_y_train.shape)
print("Reloaded y_test shape:", reloaded_y_test.shape)
```

Expected result:

```text
Reloaded X_train shape: (8844, 30)
Reloaded X_test shape: (2211, 30)
Reloaded y_train shape: (8844, 1)
Reloaded y_test shape: (2211, 1)
```

---

## Task 31 - Validate Your Work

Run this final validation cell:

```python
required_files = [
    "phishing_websites_ml_ready.csv",
    "X_train.csv",
    "X_test.csv",
    "y_train.csv",
    "y_test.csv",
    "X_train_scaled.csv",
    "X_test_scaled.csv",
    "scaler.joblib",
    "lab03_metadata.txt"
]

for file_name in required_files:
    file_path = os.path.join(output_folder, file_name)
    assert os.path.exists(file_path), f"Missing file: {file_name}"

assert df_ml.shape[0] == 11055, "The dataset should have 11,055 rows."
assert len(feature_columns) == 30, "There should be 30 feature columns."

assert X_train.shape[0] == 8844, "Training set should have 8,844 rows."
assert X_test.shape[0] == 2211, "Testing set should have 2,211 rows."

assert X_train.shape[1] == 30, "X_train should have 30 columns."
assert X_test.shape[1] == 30, "X_test should have 30 columns."

assert y_train.isnull().sum() == 0, "y_train should not contain missing values."
assert y_test.isnull().sum() == 0, "y_test should not contain missing values."

assert set(y_train.unique()).issubset({0, 1}), "y_train should only contain 0 and 1."
assert set(y_test.unique()).issubset({0, 1}), "y_test should only contain 0 and 1."

print("Validation passed. Lab 03 is complete.")
```

Expected result:

```text
Validation passed. Lab 03 is complete.
```

---

## Task 32 - Save Your Notebook

1. Select **File**.
2. Select **Save and Checkpoint**.

Confirm the notebook is named:

```text
Lab03-Prepare-Dataset-for-ML.ipynb
```

---

## Knowledge Check

Answer the following questions:

1. What is the difference between a feature column and a target column?
2. Why did you create a `target_binary` column?
3. What does `X` represent in machine learning?
4. What does `y` represent in machine learning?
5. Why should the dataset be split into training and testing sets?
6. What does `stratify=y` do during train/test splitting?
7. Why should the scaler be fitted only on the training data?
8. What is data leakage?
9. Which files from this lab will be useful in Lab 04?
10. Why is it useful to save a metadata file?

---

## Troubleshooting

### Problem: `FileNotFoundError`

Python cannot find the dataset file.

Run:

```python
import os

print(os.getcwd())
print(os.listdir())
```

Check whether one of these files exists in the current folder:

```text
phishing_websites_explored.csv
phishing_websites.csv
```

If the file is in another folder, move it into the Lab03 folder or use the recovery download code in Task 7.

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

### Problem: Target Values Are Not `-1` and `1`

The lab includes automatic category encoding if the target values are different.

Run:

```python
print(df[target_column].value_counts())
```

Review the target values before continuing.

---

### Problem: Validation Fails Because Row Counts Are Different

If you changed the dataset, removed rows, or used a different dataset version, the exact row counts may differ.

Run:

```python
print(df_ml.shape)
print(X_train.shape)
print(X_test.shape)
```

Confirm that the training and testing rows add up to the total dataset rows.

---

### Problem: Scaled Data Looks Different from Original Data

This is expected.

Scaled data is transformed so that features are easier for some machine learning algorithms to process.

Run:

```python
X_train.head()
```

Then run:

```python
X_train_scaled.head()
```

Compare the values.

---

## Summary

In this lab, you prepared the cybersecurity phishing websites dataset for machine learning.

You completed the following tasks:

- Loaded the explored dataset from Lab 02.
- Used a recovery method if the local dataset was missing.
- Reviewed dataset size and column names.
- Identified the target column.
- Encoded the target as `target_binary`.
- Separated features into `X` and labels into `y`.
- Checked for non-numeric columns.
- Checked for missing values.
- Reviewed class balance.
- Split the dataset into training and testing sets.
- Used stratified splitting to preserve class distribution.
- Scaled feature values using `StandardScaler`.
- Saved processed datasets.
- Saved the scaler.
- Created a metadata file.
- Reloaded saved files to confirm they work.
- Validated the lab output.

The dataset is now ready for model training in Lab 04.
