# Lab 01 - Load a Cybersecurity Dataset in Jupyter Notebook

## Estimated Time
**30 minutes**

## Learning Objectives
- Launch Anaconda Navigator and Jupyter Notebook on Windows.
- Create a new Python 3 notebook.
- Download and load the UCI Phishing Websites dataset using Python.
- Inspect and validate the dataset with pandas.
- Save your notebook for later use.

## Scenario
You are beginning your journey in cybersecurity data analysis. Your first task is to load and explore a real-world dataset that helps detect phishing websites. You will use Jupyter Notebook to download, load, and inspect the UCI Phishing Websites dataset, which is commonly used for cybersecurity classification tasks. By the end of this lab, you will be comfortable with basic data loading and inspection in Python.

---

## Step-by-Step Instructions

### Step 1: Open Anaconda Navigator

1. Click the **Start** menu in your Windows Server 2022 VM.
2. Search for **Anaconda Navigator** and click to launch.

> [!hint] If Anaconda Navigator is not visible, check if it was installed in your environment or use the Windows search bar.

---

### Step 2: Launch Jupyter Notebook

1. In Anaconda Navigator, find **Jupyter Notebook** and click **Launch**.
2. A browser window will open showing the Jupyter Notebook interface.

> [!alert] If the browser doesn't open automatically, check the Anaconda Navigator settings or manually open a browser and go to `http://localhost:8888`.

---

### Step 3: Create a New Python 3 Notebook

1. In the Jupyter interface, click **New** > **Python 3**.
2. Rename your notebook to **Lab01-Phishing-Dataset.ipynb**:
    - Click on the notebook title (usually "Untitled") at the top.
    - Enter the new name and click **Rename**.

---

### Step 4: Install and Import Required Packages

1. In the first cell, copy and run the following code to install and import packages:

```python
# Install the UCI repository package if needed
%pip install ucimlrepo

from ucimlrepo import fetch_ucirepo
import pandas as pd
```

> [!alert] The `%pip install ucimlrepo` command installs the package inside your notebook environment. If you see errors, check your internet connection.

---

### Step 5: Download and Load the UCI Phishing Websites Dataset

1. In a new cell, copy and run this code:

```python
# Fetch the UCI Phishing Websites dataset
phishing_websites = fetch_ucirepo(id=327)

# Load features and target
X = phishing_websites.data.features
y = phishing_websites.data.targets

# Combine features and target into one DataFrame
df = pd.concat([X, y], axis=1)

# Display basic information
print("Dataset loaded successfully")
print("Rows and columns:", df.shape)
```

> [!note] The dataset will be downloaded from the UCI Machine Learning Repository. It is less than 1 MB and should download quickly.

---

### Step 6: Display the First 5 Rows of the Dataset

1. In a new cell, run:

```python
df.head()
```

**Expected Result:**  
You should see a table with the first 5 rows and 31 columns (30 features + 1 target).

---

### Step 7: Check the Dataset Shape

1. In another cell, run:

```python
df.shape
```

**Expected Result:**  
Output should be `(11055, 31)` indicating 11,055 instances and 31 columns.

---

### Step 8: Check for Missing Values

1. In a new cell, run:

```python
df.isnull().sum()
```

**Expected Result:**  
All columns should show `0`, meaning there are no missing values.

> [!knowledge] In real-world datasets, missing values are common and require special handling. This dataset is already clean, making it ideal for beginners.

---

### Step 9: Save the Notebook

1. Click **File** > **Save Notebook** in Jupyter Notebook.
2. Confirm your notebook is named **Lab01-Phishing-Dataset.ipynb**.

> [!hint] Saving regularly ensures you do not lose your work if your session ends unexpectedly.

---

## Validation Steps

- Confirm that the notebook name is **Lab01-Phishing-Dataset.ipynb**.
- Verify that the dataset loads successfully and prints the correct shape.
- Check that `df.head()` displays data and `df.isnull().sum()` shows zero missing values.
- The notebook file should be saved and visible in the Jupyter file browser.

---

## Expected Results

- Jupyter Notebook launches and is named correctly.
- Dataset loads and displays with the correct shape: `(11055, 31)`.
- No missing values are detected.
- Notebook is saved for future work.

---

## Summary

In this lab, you learned how to launch Anaconda Navigator and Jupyter Notebook, create a new notebook, download and load a cybersecurity phishing dataset, inspect its structure, check for missing values, and save your work. These skills are foundational for cybersecurity data analysis and will help you confidently explore more datasets in the future.

---

## Troubleshooting Tips

- If you cannot launch Anaconda Navigator, try restarting your VM or reinstalling Anaconda.
- If Jupyter Notebook does not launch in your browser, check your firewall settings or open it manually at `http://localhost:8888`.
- If `%pip install ucimlrepo` fails, check your internet connection or try running `!pip install ucimlrepo`.
- If you see import errors, ensure packages are installed in the correct Python environment.
- If you do not see the correct dataset shape, review your code for typos or missing steps.

> [!alert] Always check your code for typos and run one cell at a time to catch errors early.

---

## Additional Resources

- [UCI Phishing Websites Dataset Information](https://archive.ics.uci.edu/dataset/327/phishing+websites)
- [Pandas Documentation](https://pandas.pydata.org/docs/)
- [Jupyter Notebook Beginner Guide](https://jupyter-notebook.readthedocs.io/en/stable/)
<!-- End AI generated content 1777598865217 -->

:::
