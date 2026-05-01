# Lab 02 - Explore and Visualize a Cybersecurity Dataset

## Estimated Time

45 minutes

## Learning Objectives

By the end of this lab, you will be able to:

- Open Jupyter Notebook from Anaconda.
- Continue working with the dataset created in Lab 01.
- Load a saved CSV file into pandas.
- Inspect dataset rows, columns, data types, and missing values.
- Identify the target column in the phishing dataset.
- Count the number of records in each target class.
- Create basic visualizations using matplotlib.
- Save chart images as PNG files.
- Validate that the dataset and chart files were created successfully.

---

## Scenario

In Lab 01, you downloaded the UCI Phishing Websites dataset and saved it as a local CSV file named:

```text
phishing_websites.csv
```

In this lab, you will continue as a cybersecurity data analyst. Your task is to explore the dataset before building any machine learning model.

Before training a model, analysts must understand the dataset. This includes checking the number of records, reviewing the columns, checking for missing values, understanding the target label, and visualizing the data.

---

## Before You Begin

You should have completed Lab 01.

You should have the following file from Lab 01:

```text
phishing_websites.csv
```

You should also have a notebook or folder named:

```text
Lab01
```

or a folder where you saved the CSV file.

> [!note]
> If you did not complete Lab 01, this lab includes a recovery step that can recreate the dataset.

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

## Task 2 - Create a Lab 02 Folder

In the Jupyter Notebook file browser:

1. Select **New**.
2. Select **Folder**.
3. Select the checkbox next to the new folder.
4. Select **Rename**.
5. Rename the folder to:

```text
Lab02
```

6. Open the **Lab02** folder.

---

## Task 3 - Create a New Python Notebook

1. Inside the **Lab02** folder, select **New**.
2. Select **Python 3 (ipykernel)**.
3. Rename the notebook to:

```text
Lab02-Explore-Phishing-Dataset.ipynb
```

To rename the notebook:

1. Select the notebook title at the top of the page.
2. Enter the new name.
3. Select **Rename**.

---

## Task 4 - Prepare the Python Environment

In the first notebook cell, run:

```python
%pip install pandas matplotlib ucimlrepo
```

Expected result:

```text
The required packages install successfully or show "Requirement already satisfied".
```

> [!note]
> `pandas` is used for data analysis.  
> `matplotlib` is used for charts.  
> `ucimlrepo` is used only as a backup if the CSV file from Lab 01 is missing.

---

## Task 5 - Import Required Libraries

In a new notebook cell, run:

```python
import os
import pandas as pd
import matplotlib.pyplot as plt
```

Expected result:

```text
No error messages should appear.
```

---

## Task 6 - Locate the Dataset File

In a new notebook cell, run:

```python
current_folder = os.getcwd()

print("Current folder:")
print(current_folder)

print("\nFiles in this folder:")
print(os.listdir())
```

Review the output.

You are looking for this file:

```text
phishing_websites.csv
```

If the file is not in the current folder, you have two choices:

- Move `phishing_websites.csv` into the current Lab02 folder.
- Use the recovery step in the next task.

---

## Task 7 - Load the Dataset

Run this code:

```python
csv_file = "phishing_websites.csv"

if os.path.exists(csv_file):
    df = pd.read_csv(csv_file)
    print("Dataset loaded from local CSV file.")
else:
    print("Local CSV file was not found.")
    print("Downloading the dataset again from the UCI Machine Learning Repository...")

    from ucimlrepo import fetch_ucirepo

    phishing_websites = fetch_ucirepo(id=327)

    X = phishing_websites.data.features
    y = phishing_websites.data.targets

    df = pd.concat([X, y], axis=1)

    df.to_csv(csv_file, index=False)

    print("Dataset downloaded and saved as phishing_websites.csv.")

print("Rows and columns:", df.shape)
df.head()
```

Expected result:

```text
Rows and columns: (11055, 31)
```

> [!note]
> The dataset has 11,055 rows. It contains 30 feature columns and 1 target column.

---

## Task 8 - Inspect the First Five Rows

Run this code:

```python
df.head()
```

Review the table.

Each row represents one website. The columns describe characteristics of the website, such as URL behavior, domain information, and other features that may help classify phishing websites.

---

## Task 9 - Check the Dataset Size

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

---

## Task 10 - View Column Names

Run this code:

```python
for index, column in enumerate(df.columns, start=1):
    print(index, column)
```

Expected result:

```text
A numbered list of column names should appear.
```

This helps you understand what features are available in the dataset.

---

## Task 11 - Display Dataset Information

Run this code:

```python
df.info()
```

Review the output.

Look for:

- Number of entries
- Number of columns
- Column data types
- Non-null counts

Expected result:

```text
The dataset should show 11055 entries and 31 columns.
```

---

## Task 12 - Check for Missing Values

Run this code:

```python
missing_by_column = df.isnull().sum()
total_missing = missing_by_column.sum()

print("Missing values by column:")
print(missing_by_column)

print("\nTotal missing values:", total_missing)
```

Expected result:

```text
Total missing values: 0
```

> [!knowledge]
> Missing values can cause problems during data analysis and machine learning. A missing value means that a record has an empty or unavailable value for a column.

---

## Task 13 - Generate Summary Statistics

Run this code:

```python
df.describe()
```

Review the output.

The summary table shows statistics such as:

- Count
- Mean
- Standard deviation
- Minimum value
- Maximum value

---

## Task 14 - Identify the Target Column

The target column is the column that contains the class label.

In this dataset, the target column is usually the last column.

Run this code:

```python
target_column = df.columns[-1]

print("Target column:", target_column)
```

Expected result:

```text
The target column name should display.
```

---

## Task 15 - Count the Target Classes

Run this code:

```python
target_counts = df[target_column].value_counts()

print("Target class counts:")
print(target_counts)
```

Expected result:

```text
A count of each target class should display.
```

The target values represent website classes. Depending on the dataset version, the labels may be numeric values such as:

```text
-1
1
```

These values represent different website categories, such as phishing or legitimate.

---

## Task 16 - Create a Target Distribution Bar Chart

Run this code:

```python
plt.figure(figsize=(6, 4))

target_counts.plot(kind="bar")

plt.title("Target Class Distribution")
plt.xlabel("Target Class")
plt.ylabel("Number of Websites")
plt.xticks(rotation=0)
plt.tight_layout()

plt.savefig("target_distribution.png")
plt.show()
```

Expected result:

```text
A bar chart should appear.
```

This chart shows how many websites belong to each target class.

---

## Task 17 - Confirm the Chart Was Saved

Run this code:

```python
chart_file = "target_distribution.png"

print("Chart file exists:", os.path.exists(chart_file))
```

Expected result:

```text
Chart file exists: True
```

---

## Task 18 - Select Features for Visualization

Instead of asking you to guess a column name, this lab will automatically select the first six feature columns.

Run this code:

```python
feature_columns = list(df.columns[:-1])[:6]

print("Selected feature columns:")
for column in feature_columns:
    print("-", column)
```

Expected result:

```text
Six feature column names should display.
```

---

## Task 19 - Create Histograms for Selected Features

Run this code:

```python
df[feature_columns].hist(figsize=(10, 8))

plt.suptitle("Histograms of Selected Website Features")
plt.tight_layout()

plt.savefig("selected_feature_histograms.png")
plt.show()
```

Expected result:

```text
A group of histograms should appear.
```

The histograms show how values are distributed for selected website features.

---

## Task 20 - Confirm the Histogram File Was Saved

Run this code:

```python
histogram_file = "selected_feature_histograms.png"

print("Histogram file exists:", os.path.exists(histogram_file))
```

Expected result:

```text
Histogram file exists: True
```

---

## Task 21 - Compare Feature Averages by Target Class

Run this code:

```python
grouped_means = df.groupby(target_column)[feature_columns].mean()

grouped_means
```

Expected result:

```text
A table should display the average value of selected features for each target class.
```

This helps you compare how website features differ between classes.

---

## Task 22 - Create a Feature Comparison Chart

Run this code:

```python
grouped_means.T.plot(kind="bar", figsize=(10, 5))

plt.title("Average Feature Values by Target Class")
plt.xlabel("Feature")
plt.ylabel("Average Value")
plt.xticks(rotation=45, ha="right")
plt.tight_layout()

plt.savefig("feature_comparison_by_target.png")
plt.show()
```

Expected result:

```text
A bar chart should appear comparing selected feature averages by target class.
```

---

## Task 23 - Confirm the Feature Comparison Chart Was Saved

Run this code:

```python
comparison_file = "feature_comparison_by_target.png"

print("Feature comparison chart exists:", os.path.exists(comparison_file))
```

Expected result:

```text
Feature comparison chart exists: True
```

---

## Task 24 - Create a Simple Correlation Table

Correlation helps identify relationships between numeric columns.

Run this code:

```python
correlation_table = df[feature_columns].corr()

correlation_table
```

Expected result:

```text
A correlation table should display.
```

Values close to `1` indicate a strong positive relationship.  
Values close to `-1` indicate a strong negative relationship.  
Values close to `0` indicate a weak relationship.

---

## Task 25 - Save a Cleaned Copy for Later Labs

In later labs, you may use this explored dataset for machine learning.

Run this code:

```python
df.to_csv("phishing_websites_explored.csv", index=False)

print("Saved phishing_websites_explored.csv")
```

Expected result:

```text
Saved phishing_websites_explored.csv
```

---

## Task 26 - Validate Your Work

Run this final validation cell:

```python
assert df.shape[0] == 11055, "The dataset should have 11,055 rows."
assert df.shape[1] == 31, "The dataset should have 31 columns."
assert df.isnull().sum().sum() == 0, "The dataset should not contain missing values."

assert os.path.exists("phishing_websites.csv"), "The original CSV file was not found."
assert os.path.exists("target_distribution.png"), "The target distribution chart was not created."
assert os.path.exists("selected_feature_histograms.png"), "The histogram chart was not created."
assert os.path.exists("feature_comparison_by_target.png"), "The feature comparison chart was not created."
assert os.path.exists("phishing_websites_explored.csv"), "The explored CSV file was not created."

print("Validation passed. Lab 02 is complete.")
```

Expected result:

```text
Validation passed. Lab 02 is complete.
```

---

## Task 27 - Save Your Notebook

1. Select **File**.
2. Select **Save and Checkpoint**.

Confirm the notebook is named:

```text
Lab02-Explore-Phishing-Dataset.ipynb
```

---

## Knowledge Check

Answer the following questions:

1. How many rows are in the phishing websites dataset?
2. How many columns are in the dataset?
3. Why is it important to check for missing values?
4. What does the target distribution chart show?
5. Why might class distribution matter in cybersecurity machine learning?
6. What is the purpose of saving chart images?
7. What file did you save for use in later labs?

---

## Troubleshooting

### Problem: `FileNotFoundError`

This means Python could not find the CSV file.

Run this code to see which folder your notebook is using:

```python
import os

print(os.getcwd())
print(os.listdir())
```

Make sure `phishing_websites.csv` is in that folder.

---

### Problem: `ModuleNotFoundError`

If you see an error such as:

```text
ModuleNotFoundError: No module named 'pandas'
```

or:

```text
ModuleNotFoundError: No module named 'matplotlib'
```

run this cell:

```python
%pip install pandas matplotlib ucimlrepo
```

Then restart the kernel:

1. Select **Kernel**.
2. Select **Restart Kernel**.
3. Run the notebook cells again from the beginning.

---

### Problem: Dataset Download Fails

If the local CSV file is missing and the recovery download fails, check that the lab VM has internet access.

Run this test:

```python
import urllib.request

urllib.request.urlopen("https://archive.ics.uci.edu")

print("Internet connection test successful.")
```

If this fails, verify the lab environment networking settings.

---

### Problem: Chart Does Not Display

Make sure this line was imported:

```python
import matplotlib.pyplot as plt
```

Then rerun the chart cell.

---

### Problem: Chart File Was Not Created

Run this code:

```python
import os

print(os.listdir())
```

Look for these files:

```text
target_distribution.png
selected_feature_histograms.png
feature_comparison_by_target.png
```

If they are missing, rerun the chart cells.

---

## Summary

In this lab, you explored and visualized the cybersecurity phishing websites dataset.

You completed the following tasks:

- Opened Jupyter Notebook.
- Created a Lab 02 notebook.
- Loaded the dataset from `phishing_websites.csv`.
- Used a recovery method if the CSV file was missing.
- Inspected rows, columns, data types, and missing values.
- Identified the target column.
- Counted target class values.
- Created a target distribution chart.
- Created histograms for selected features.
- Compared average feature values by target class.
- Created a basic correlation table.
- Saved chart images.
- Saved an explored CSV file for future labs.
- Validated your work.

You are now ready to prepare the dataset for machine learning in the next lab.
