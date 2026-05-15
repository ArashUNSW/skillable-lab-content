# Lab 01 - Load a Cybersecurity Dataset in Jupyter Notebook

## Estimated Time

30 minutes

## Learning Objectives

By the end of this lab, you will be able to:

- Launch Anaconda Navigator and Jupyter Notebook on Windows.
- Create a new Python 3 notebook.
- Install and import Python packages.
- Download a small cybersecurity dataset using Python.
- Load the dataset into a pandas DataFrame.
- Inspect dataset rows, columns, and missing values.
- Save a local CSV copy of the dataset.
- Save your completed Jupyter Notebook.

---

## Scenario

You are starting a cybersecurity data analysis project. Before you can build a machine learning model or perform deeper analysis, you need to load and inspect a dataset.

In this lab, you will use the **UCI Phishing Websites dataset**. This dataset contains website characteristics that can be used to classify whether a website is phishing or legitimate.

You will use Python, pandas, and Jupyter Notebook to download, load, inspect, and save the dataset.

---

## Task 1 - Launch Anaconda Navigator

1. Select the **Windows Start** menu.
2. Search for:

```text
Anaconda Navigator
```

3. Open **Anaconda Navigator**.
4. Wait for Anaconda Navigator to load.

> [!note]
> Anaconda Navigator may take a few moments to open the first time.

---

## Task 2 - Launch Jupyter Notebook

1. In **Anaconda Navigator**, locate **Jupyter Notebook**.
2. Select **Launch**.
3. Wait for Jupyter Notebook to open in your web browser.

> [!hint]
> Jupyter Notebook usually opens in a browser window automatically.

If Jupyter Notebook does not open from Anaconda Navigator, use this alternative method:

1. Open the **Windows Start** menu.
2. Search for:

```text
Anaconda Prompt
```

3. Open **Anaconda Prompt**.
4. Run the following command:

```powershell
jupyter notebook
```

5. Wait for Jupyter Notebook to open in the browser.

---

## Task 3 - Create a Lab Folder

In the Jupyter Notebook file browser, create a new folder for this lab.

1. In the Jupyter Notebook browser page, select **New**.
2. Select **Folder**.
3. Select the new folder checkbox.
4. Select **Rename**.
5. Rename the folder to:

```text
Lab01
```

6. Open the **Lab01** folder.

---

## Task 4 - Create a New Python Notebook

1. Inside the **Lab01** folder, select **New**.
2. Select **Python 3 (ipykernel)**.

A new notebook opens.

3. Select the notebook title at the top of the page.
4. Rename the notebook to:

```text
Lab01-Phishing-Dataset.ipynb
```

5. Select **Rename**.

---

## Task 5 - Install Required Python Packages

In the first notebook cell, enter the following code:

```python
%pip install ucimlrepo pandas
```

Run the cell.

To run a cell, press:

```text
Shift + Enter
```

Wait until the installation completes.

You may see output similar to this:

```text
Requirement already satisfied
```

That message is OK. It means the package is already installed.

> [!note]
> The `ucimlrepo` package allows Python to download datasets directly from the UCI Machine Learning Repository.

---

## Task 6 - Import Python Libraries

In a new notebook cell, enter and run this code:

```python
from ucimlrepo import fetch_ucirepo
import pandas as pd
```

Expected result:

```text
No error messages should appear.
```

---

## Task 7 - Download the Cybersecurity Dataset

In a new notebook cell, enter and run this code:

```python
# Download the UCI Phishing Websites dataset
phishing_websites = fetch_ucirepo(id=327)

print("Dataset downloaded successfully.")
```

Expected result:

```text
Dataset downloaded successfully.
```

> [!alert]
> This step requires internet access. If the download fails, check that the lab virtual machine has internet access.

---

## Task 8 - Load the Dataset into pandas

In a new notebook cell, enter and run this code:

```python
# Load the feature columns
X = phishing_websites.data.features

# Load the target column
y = phishing_websites.data.targets

# Combine features and target into one DataFrame
df = pd.concat([X, y], axis=1)

print("Dataset loaded successfully.")
print("Rows and columns:", df.shape)
```

Expected result:

```text
Dataset loaded successfully.
Rows and columns: (11055, 31)
```

> [!note]
> The dataset contains 11,055 rows. It has 30 feature columns and 1 target column.

---

## Task 9 - Display the First Five Rows

In a new notebook cell, enter and run this code:

```python
df.head()
```

Expected result:

```text
The first five rows of the dataset should display in a table.
```

Review the output table. Each row represents a website. The columns describe characteristics of that website.

---

## Task 10 - Check the Dataset Shape

In a new notebook cell, enter and run this code:

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

## Task 11 - View the Column Names

In a new notebook cell, enter and run this code:

```python
df.columns
```

Expected result:

```text
A list of dataset column names should display.
```

The column names describe website characteristics that can help identify phishing websites.

---

## Task 12 - Display Dataset Information

In a new notebook cell, enter and run this code:

```python
df.info()
```

Expected result:

```text
The notebook should display column names, data types, and non-null counts.
```

Review the output. This helps confirm that the data loaded correctly.

---

## Task 13 - Check for Missing Values

In a new notebook cell, enter and run this code:

```python
missing_values = df.isnull().sum().sum()

print("Total missing values:", missing_values)
```

Expected result:

```text
Total missing values: 0
```

> [!note]
> Missing values are empty or unavailable values in a dataset. Checking for missing values is an important early step in data analysis.

---

## Task 14 - View Basic Statistics

In a new notebook cell, enter and run this code:

```python
df.describe()
```

Expected result:

```text
A statistical summary table should display.
```

The summary table shows values such as count, mean, standard deviation, minimum, and maximum for the dataset columns.

---

## Task 15 - Identify the Target Column

In a new notebook cell, enter and run this code:

```python
print("Target columns:")
print(y.columns)
```

Expected result:

```text
The target column name should display.
```

The target column contains the classification label for each website.

---

## Task 16 - Count the Target Values

In a new notebook cell, enter and run this code:

```python
target_column = y.columns[0]

print("Target column:", target_column)
print(df[target_column].value_counts())
```

Expected result:

```text
A count of each target value should display.
```

This shows how many records belong to each class.

---

## Task 17 - Save the Dataset as a CSV File

In a new notebook cell, enter and run this code:

```python
df.to_csv("phishing_websites.csv", index=False)

print("Dataset saved as phishing_websites.csv")
```

Expected result:

```text
Dataset saved as phishing_websites.csv
```

This creates a local CSV file in the same folder as your notebook.

---

## Task 18 - Confirm the CSV File Was Created

In a new notebook cell, enter and run this code:

```python
import os

file_exists = os.path.exists("phishing_websites.csv")

print("CSV file exists:", file_exists)
```

Expected result:

```text
CSV file exists: True
```

---

## Task 19 - Reload the CSV File

In a new notebook cell, enter and run this code:

```python
df_csv = pd.read_csv("phishing_websites.csv")

print("CSV file loaded successfully.")
print("Rows and columns:", df_csv.shape)

df_csv.head()
```

Expected result:

```text
CSV file loaded successfully.
Rows and columns: (11055, 31)
```

The first five rows should display again.

---

## Task 20 - Validate Your Work

Run this final validation code:

```python
assert df.shape[0] == 11055, "The dataset should have 11,055 rows."
assert df.shape[1] == 31, "The dataset should have 30 features plus 1 target column."
assert df.isnull().sum().sum() == 0, "The dataset should not contain missing values."
assert os.path.exists("phishing_websites.csv") == True, "The CSV file was not created."

print("Validation passed. Lab 01 is complete.")
```

Expected result:

```text
Validation passed. Lab 01 is complete.
```

---

## Task 21 - Save Your Notebook

1. In Jupyter Notebook, select **File**.
2. Select **Save and Checkpoint**.

Confirm your notebook is named:

```text
Lab01-Phishing-Dataset.ipynb
```

---

## Troubleshooting

### Problem: Jupyter Notebook does not open

Open **Anaconda Prompt** and run:

```powershell
jupyter notebook
```

Wait for the browser window to open.

---

### Problem: `ModuleNotFoundError: No module named 'ucimlrepo'`

Run this cell again:

```python
%pip install ucimlrepo
```

Then restart the notebook kernel:

1. Select **Kernel**.
2. Select **Restart Kernel**.
3. Run the cells again from the beginning.

---

### Problem: Dataset download fails

Check that the lab virtual machine has internet access.

Then run this test in a notebook cell:

```python
import urllib.request

url = "https://archive.ics.uci.edu"
response = urllib.request.urlopen(url)

print("Internet connection test successful.")
```

If the test fails, verify the lab environment has internet access.

---

### Problem: `phishing_websites.csv` is not found

Make sure you ran this cell:

```python
df.to_csv("phishing_websites.csv", index=False)
```

Then run this again:

```python
import os

print(os.path.exists("phishing_websites.csv"))
```

Expected result:

```text
True
```

---

## Knowledge Check

Answer the following questions:

1. What tool did you use to create and run Python code interactively?
2. What Python package was used to load the dataset into a DataFrame?
3. How many rows are in the dataset?
4. How many columns are in the dataset after combining features and target?
5. Why is it important to check for missing values?

---

## Summary

In this lab, you completed the following tasks:

- Launched Anaconda Navigator.
- Opened Jupyter Notebook.
- Created a new Python notebook.
- Installed required Python packages.
- Downloaded the UCI Phishing Websites dataset.
- Loaded the dataset into pandas.
- Displayed the first five rows.
- Checked the number of rows and columns.
- Checked for missing values.
- Saved the dataset as a CSV file.
- Validated your work.
- Saved your notebook.

You are now ready to use this dataset for further cybersecurity data analysis in later labs.
