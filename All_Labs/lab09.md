# Lab 09 - Unsupervised Learning for Cybersecurity Anomaly Detection

## Estimated Time

75 minutes

## Learning Objectives

By the end of this lab, you will be able to:

- Explain the difference between supervised and unsupervised learning.
- Use the prepared phishing website dataset from previous labs.
- Train a clustering model without using target labels.
- Use K-Means clustering to group website records.
- Use PCA to reduce feature dimensions for visualization.
- Use Isolation Forest for anomaly detection.
- Compare unsupervised results with known labels after the analysis.
- Identify suspicious records using anomaly scores.
- Save clustering, anomaly detection, visualization, and summary outputs.
- Validate that all Lab 09 artifacts were created successfully.

---

## Scenario

You are working as a cybersecurity data analyst.

In previous labs, you trained supervised machine learning models. Supervised models learn from data that already has known labels, such as:

```text
0 = legitimate or benign
1 = phishing or suspicious
```

In this lab, you will use **unsupervised learning**.

Unsupervised learning does not use the target label while training. Instead, it tries to discover patterns, groups, or unusual records from the feature data.

This is useful in cybersecurity because analysts often need to detect unusual behavior even when labeled attack data is not available.

---

## What You Will Create

In this lab, you will create the following files:

```text
unsupervised/kmeans_cluster_assignments.csv
unsupervised/kmeans_cluster_summary.csv
unsupervised/kmeans_cluster_vs_target.csv
unsupervised/pca_projection.csv
unsupervised/kmeans_pca_clusters.png
unsupervised/isolation_forest_results.csv
unsupervised/isolation_forest_summary.csv
unsupervised/isolation_forest_vs_target.csv
unsupervised/isolation_forest_pca_anomalies.png
unsupervised/top_anomaly_examples.csv
unsupervised/lab09_unsupervised_summary.txt
unsupervised/lab09_metadata.txt
```

These files document the unsupervised learning process and results.

---

## Before You Begin

You should have completed Labs 01 through 08.

You need the processed data files created in Lab 03 and reused in later labs.

The required files are:

```text
processed/X_train.csv
processed/X_test.csv
processed/y_train.csv
processed/y_test.csv
processed/X_train_scaled.csv
processed/X_test_scaled.csv
```

> [!note]
> This lab includes copy logic to find the `processed` folder from previous labs if it is not already in the Lab09 folder.

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

## Task 2 - Create a Lab 09 Folder

In the Jupyter Notebook file browser:

1. Select **New**.
2. Select **Folder**.
3. Select the checkbox beside the new folder.
4. Select **Rename**.
5. Rename the folder to:

```text
Lab09
```

6. Open the **Lab09** folder.

---

## Task 3 - Create a New Python Notebook

1. Inside the **Lab09** folder, select **New**.
2. Select **Python 3 (ipykernel)**.
3. Rename the notebook to:

```text
Lab09-Unsupervised-Anomaly-Detection.ipynb
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

from sklearn.cluster import KMeans
from sklearn.decomposition import PCA
from sklearn.ensemble import IsolationForest
from sklearn.metrics import adjusted_rand_score, normalized_mutual_info_score
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
The current Lab09 folder path should display.
```

---

## Task 7 - Locate or Copy the Processed Files

This step looks for the `processed` folder.

If it is not in the Lab09 folder, the code attempts to copy it from previous lab folders.

Run this code:

```python
processed_folder = "processed"

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
        print(f"{destination_folder} folder found in the current Lab09 folder.")
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
        os.path.join("..", "Lab08", "processed"),
        os.path.join("..", "Lab06", "processed"),
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
> If the processed files are not found, return to Lab 03 and complete the validation step.

---

## Task 8 - Create an Unsupervised Output Folder

Run this code:

```python
unsupervised_folder = "unsupervised"

os.makedirs(unsupervised_folder, exist_ok=True)

print("Unsupervised output folder created:", unsupervised_folder)
```

Expected result:

```text
Unsupervised output folder created: unsupervised
```

---

## Task 9 - Load the Prepared Dataset

Run this code:

```python
X_train = pd.read_csv(os.path.join(processed_folder, "X_train.csv"))
X_test = pd.read_csv(os.path.join(processed_folder, "X_test.csv"))

X_train_scaled = pd.read_csv(os.path.join(processed_folder, "X_train_scaled.csv"))
X_test_scaled = pd.read_csv(os.path.join(processed_folder, "X_test_scaled.csv"))

y_train = pd.read_csv(os.path.join(processed_folder, "y_train.csv")).squeeze("columns")
y_test = pd.read_csv(os.path.join(processed_folder, "y_test.csv")).squeeze("columns")

print("X_train shape:", X_train.shape)
print("X_test shape:", X_test.shape)
print("X_train_scaled shape:", X_train_scaled.shape)
print("X_test_scaled shape:", X_test_scaled.shape)
print("y_train shape:", y_train.shape)
print("y_test shape:", y_test.shape)
```

Expected result:

```text
X_train shape: (8844, 30)
X_test shape: (2211, 30)
X_train_scaled shape: (8844, 30)
X_test_scaled shape: (2211, 30)
y_train shape: (8844,)
y_test shape: (2211,)
```

---

## Task 10 - Combine the Dataset for Unsupervised Learning

For unsupervised learning, you will combine the training and testing features.

The model will use the feature columns only.

The target label will be saved separately and used only after clustering or anomaly detection to help interpret the results.

Run this code:

```python
X_all = pd.concat([X_train, X_test], axis=0).reset_index(drop=True)
X_all_scaled = pd.concat([X_train_scaled, X_test_scaled], axis=0).reset_index(drop=True)
y_all = pd.concat([y_train, y_test], axis=0).reset_index(drop=True)

print("Combined unscaled feature shape:", X_all.shape)
print("Combined scaled feature shape:", X_all_scaled.shape)
print("Combined target shape:", y_all.shape)
```

Expected result:

```text
Combined unscaled feature shape: (11055, 30)
Combined scaled feature shape: (11055, 30)
Combined target shape: (11055,)
```

> [!note]
> The target label is not used to train the unsupervised models. It is only used later to compare results.

---

## Task 11 - Review Supervised vs. Unsupervised Learning

Supervised learning uses labeled examples.

Example:

```text
Website features + known label
```

Unsupervised learning uses only the input features.

Example:

```text
Website features only
```

In this lab:

```text
K-Means clustering = groups similar website records
Isolation Forest = identifies unusual website records
```

---

## Task 12 - Train a K-Means Clustering Model

K-Means is an unsupervised clustering algorithm.

Because the known dataset has two major classes, this lab starts with:

```text
n_clusters = 2
```

Run this code:

```python
kmeans_model = KMeans(
    n_clusters=2,
    random_state=42,
    n_init=10
)

kmeans_clusters = kmeans_model.fit_predict(X_all_scaled)

print("K-Means clustering complete.")
print("Number of cluster assignments:", len(kmeans_clusters))
```

Expected result:

```text
K-Means clustering complete.
Number of cluster assignments: 11055
```

> [!knowledge]
> K-Means does not know which records are phishing or legitimate. It only groups records based on similarity.

---

## Task 13 - Review K-Means Cluster Counts

Run this code:

```python
cluster_counts = pd.Series(kmeans_clusters).value_counts().sort_index()

print("K-Means cluster counts:")
print(cluster_counts)
```

Expected result:

```text
The number of records in each cluster should display.
```

---

## Task 14 - Create a K-Means Cluster Assignment Table

Run this code:

```python
kmeans_results_df = X_all.copy()

kmeans_results_df["known_target"] = y_all
kmeans_results_df["kmeans_cluster"] = kmeans_clusters

kmeans_results_df.head()
```

Expected result:

```text
A table should display website features, known target, and cluster assignment.
```

Remember:

```text
known_target is used only for interpretation after clustering.
```

---

## Task 15 - Save K-Means Cluster Assignments

Run this code:

```python
kmeans_assignments_file = os.path.join(
    unsupervised_folder,
    "kmeans_cluster_assignments.csv"
)

kmeans_results_df.to_csv(kmeans_assignments_file, index=False)

print("Saved:", kmeans_assignments_file)
```

Expected result:

```text
Saved: unsupervised\kmeans_cluster_assignments.csv
```

---

## Task 16 - Compare K-Means Clusters with Known Target Labels

Although K-Means did not use target labels, you can compare clusters with known labels afterward.

Run this code:

```python
kmeans_vs_target = pd.crosstab(
    kmeans_results_df["kmeans_cluster"],
    kmeans_results_df["known_target"],
    rownames=["K-Means Cluster"],
    colnames=["Known Target"]
)

kmeans_vs_target
```

Expected result:

```text
A table should show how known target values are distributed across clusters.
```

---

## Task 17 - Save K-Means vs. Target Table

Run this code:

```python
kmeans_vs_target_file = os.path.join(
    unsupervised_folder,
    "kmeans_cluster_vs_target.csv"
)

kmeans_vs_target.to_csv(kmeans_vs_target_file)

print("Saved:", kmeans_vs_target_file)
```

Expected result:

```text
Saved: unsupervised\kmeans_cluster_vs_target.csv
```

---

## Task 18 - Calculate Clustering Comparison Scores

These metrics compare unsupervised clusters with the known target labels.

The target labels were not used during training.

Run this code:

```python
adjusted_rand = adjusted_rand_score(y_all, kmeans_clusters)
normalized_mutual_info = normalized_mutual_info_score(y_all, kmeans_clusters)

print("Adjusted Rand Index:", round(adjusted_rand, 4))
print("Normalized Mutual Information:", round(normalized_mutual_info, 4))
```

Expected result:

```text
Two clustering comparison scores should display.
```

> [!note]
> These scores help compare clusters to known labels, but unsupervised learning does not require labels.

---

## Task 19 - Create a K-Means Cluster Summary

Run this code:

```python
kmeans_cluster_summary = kmeans_results_df.groupby("kmeans_cluster").agg(
    record_count=("kmeans_cluster", "count"),
    target_mean=("known_target", "mean")
).reset_index()

kmeans_cluster_summary["target_mean"] = kmeans_cluster_summary["target_mean"].round(4)

kmeans_cluster_summary
```

Expected result:

```text
A summary table should display cluster counts and average known target value.
```

If `target_mean` is higher, that cluster may contain more phishing or suspicious records.

---

## Task 20 - Save the K-Means Cluster Summary

Run this code:

```python
kmeans_summary_file = os.path.join(
    unsupervised_folder,
    "kmeans_cluster_summary.csv"
)

kmeans_cluster_summary.to_csv(kmeans_summary_file, index=False)

print("Saved:", kmeans_summary_file)
```

Expected result:

```text
Saved: unsupervised\kmeans_cluster_summary.csv
```

---

## Task 21 - Reduce Features with PCA

The dataset has 30 feature columns.

PCA can reduce the dataset to two dimensions for visualization.

Run this code:

```python
pca = PCA(n_components=2, random_state=42)

pca_projection = pca.fit_transform(X_all_scaled)

pca_df = pd.DataFrame(
    pca_projection,
    columns=["PC1", "PC2"]
)

pca_df["known_target"] = y_all
pca_df["kmeans_cluster"] = kmeans_clusters

print("PCA projection shape:", pca_df.shape)
print("Explained variance ratio:", pca.explained_variance_ratio_)

pca_df.head()
```

Expected result:

```text
PCA projection shape: (11055, 4)
```

---

## Task 22 - Save the PCA Projection

Run this code:

```python
pca_projection_file = os.path.join(
    unsupervised_folder,
    "pca_projection.csv"
)

pca_df.to_csv(pca_projection_file, index=False)

print("Saved:", pca_projection_file)
```

Expected result:

```text
Saved: unsupervised\pca_projection.csv
```

---

## Task 23 - Visualize K-Means Clusters with PCA

Run this code:

```python
plt.figure(figsize=(8, 6))

plt.scatter(
    pca_df["PC1"],
    pca_df["PC2"],
    c=pca_df["kmeans_cluster"],
    s=10,
    alpha=0.6
)

plt.title("K-Means Clusters Visualized with PCA")
plt.xlabel("Principal Component 1")
plt.ylabel("Principal Component 2")
plt.tight_layout()

kmeans_pca_chart_file = os.path.join(
    unsupervised_folder,
    "kmeans_pca_clusters.png"
)

plt.savefig(kmeans_pca_chart_file)
plt.show()

print("Saved:", kmeans_pca_chart_file)
```

Expected result:

```text
A scatter plot should display and be saved.
```

---

## Task 24 - Understand Anomaly Detection

Anomaly detection looks for unusual records.

In cybersecurity, anomalies may represent unusual or suspicious activity.

In this lab, you will use:

```text
Isolation Forest
```

Isolation Forest is an unsupervised anomaly detection algorithm.

It tries to identify records that are easier to isolate from the rest of the data.

---

## Task 25 - Train an Isolation Forest Model

This lab uses a contamination value of:

```text
0.10
```

This means the model is asked to flag about 10% of records as anomalies.

Run this code:

```python
isolation_forest_model = IsolationForest(
    n_estimators=100,
    contamination=0.10,
    random_state=42
)

isolation_forest_model.fit(X_all_scaled)

isolation_predictions_raw = isolation_forest_model.predict(X_all_scaled)

print("Isolation Forest training complete.")
print("Raw prediction values:")
print(pd.Series(isolation_predictions_raw).value_counts())
```

Expected result:

```text
Isolation Forest training complete.
Raw prediction values should display.
```

Isolation Forest returns:

```text
 1 = normal record
-1 = anomaly
```

---

## Task 26 - Convert Isolation Forest Predictions

Run this code:

```python
isolation_anomaly_flag = (isolation_predictions_raw == -1).astype(int)

print("Anomaly flag counts:")
print(pd.Series(isolation_anomaly_flag).value_counts())
```

Expected result:

```text
Anomaly flag counts should display.
```

In this lab:

```text
1 = anomaly
0 = normal
```

---

## Task 27 - Calculate Isolation Forest Anomaly Scores

Run this code:

```python
anomaly_scores = -isolation_forest_model.score_samples(X_all_scaled)

isolation_results_df = X_all.copy()

isolation_results_df["known_target"] = y_all
isolation_results_df["anomaly_flag"] = isolation_anomaly_flag
isolation_results_df["anomaly_score"] = anomaly_scores

isolation_results_df.head()
```

Expected result:

```text
A table should display features, known target, anomaly flag, and anomaly score.
```

Higher anomaly scores indicate records that are more unusual according to the model.

---

## Task 28 - Save Isolation Forest Results

Run this code:

```python
isolation_results_file = os.path.join(
    unsupervised_folder,
    "isolation_forest_results.csv"
)

isolation_results_df.to_csv(isolation_results_file, index=False)

print("Saved:", isolation_results_file)
```

Expected result:

```text
Saved: unsupervised\isolation_forest_results.csv
```

---

## Task 29 - Compare Isolation Forest Results with Known Targets

Run this code:

```python
isolation_vs_target = pd.crosstab(
    isolation_results_df["anomaly_flag"],
    isolation_results_df["known_target"],
    rownames=["Anomaly Flag"],
    colnames=["Known Target"]
)

isolation_vs_target
```

Expected result:

```text
A table should show how known targets are distributed across anomaly flags.
```

Remember:

```text
known_target was not used to train Isolation Forest.
```

---

## Task 30 - Save Isolation Forest vs. Target Table

Run this code:

```python
isolation_vs_target_file = os.path.join(
    unsupervised_folder,
    "isolation_forest_vs_target.csv"
)

isolation_vs_target.to_csv(isolation_vs_target_file)

print("Saved:", isolation_vs_target_file)
```

Expected result:

```text
Saved: unsupervised\isolation_forest_vs_target.csv
```

---

## Task 31 - Create an Isolation Forest Summary

Run this code:

```python
isolation_summary = isolation_results_df.groupby("anomaly_flag").agg(
    record_count=("anomaly_flag", "count"),
    average_known_target=("known_target", "mean"),
    average_anomaly_score=("anomaly_score", "mean")
).reset_index()

isolation_summary["average_known_target"] = isolation_summary["average_known_target"].round(4)
isolation_summary["average_anomaly_score"] = isolation_summary["average_anomaly_score"].round(4)

isolation_summary
```

Expected result:

```text
A summary table should display anomaly counts and average scores.
```

---

## Task 32 - Save the Isolation Forest Summary

Run this code:

```python
isolation_summary_file = os.path.join(
    unsupervised_folder,
    "isolation_forest_summary.csv"
)

isolation_summary.to_csv(isolation_summary_file, index=False)

print("Saved:", isolation_summary_file)
```

Expected result:

```text
Saved: unsupervised\isolation_forest_summary.csv
```

---

## Task 33 - Visualize Isolation Forest Anomalies with PCA

Add the anomaly results to the PCA table.

Run this code:

```python
pca_df["anomaly_flag"] = isolation_anomaly_flag
pca_df["anomaly_score"] = anomaly_scores

plt.figure(figsize=(8, 6))

normal_points = pca_df[pca_df["anomaly_flag"] == 0]
anomaly_points = pca_df[pca_df["anomaly_flag"] == 1]

plt.scatter(
    normal_points["PC1"],
    normal_points["PC2"],
    s=10,
    alpha=0.5,
    label="Normal"
)

plt.scatter(
    anomaly_points["PC1"],
    anomaly_points["PC2"],
    s=20,
    alpha=0.8,
    label="Anomaly"
)

plt.title("Isolation Forest Anomalies Visualized with PCA")
plt.xlabel("Principal Component 1")
plt.ylabel("Principal Component 2")
plt.legend()
plt.tight_layout()

isolation_pca_chart_file = os.path.join(
    unsupervised_folder,
    "isolation_forest_pca_anomalies.png"
)

plt.savefig(isolation_pca_chart_file)
plt.show()

print("Saved:", isolation_pca_chart_file)
```

Expected result:

```text
A scatter plot showing normal and anomaly points should display and be saved.
```

---

## Task 34 - Review the Top Anomaly Examples

Run this code:

```python
top_anomaly_examples = isolation_results_df.sort_values(
    by="anomaly_score",
    ascending=False
).head(20)

top_anomaly_examples
```

Expected result:

```text
A table of the top 20 anomaly examples should display.
```

These are the records that Isolation Forest considered most unusual.

---

## Task 35 - Save Top Anomaly Examples

Run this code:

```python
top_anomaly_examples_file = os.path.join(
    unsupervised_folder,
    "top_anomaly_examples.csv"
)

top_anomaly_examples.to_csv(top_anomaly_examples_file, index=False)

print("Saved:", top_anomaly_examples_file)
```

Expected result:

```text
Saved: unsupervised\top_anomaly_examples.csv
```

---

## Task 36 - Compare Clustering and Anomaly Detection

Run this code:

```python
combined_unsupervised_summary = pd.DataFrame({
    "method": [
        "K-Means Clustering",
        "Isolation Forest"
    ],
    "main_output": [
        "Cluster assignment",
        "Anomaly flag and anomaly score"
    ],
    "uses_target_during_training": [
        False,
        False
    ],
    "best_use_case": [
        "Grouping similar website records",
        "Finding unusual website records"
    ]
})

combined_unsupervised_summary
```

Expected result:

```text
A comparison table should display.
```

---

## Task 37 - Create a Lab 09 Summary File

Run this code:

```python
summary_file = os.path.join(
    unsupervised_folder,
    "lab09_unsupervised_summary.txt"
)

with open(summary_file, "w") as file:
    file.write("Lab 09 - Unsupervised Learning for Cybersecurity Anomaly Detection\n")
    file.write("==================================================================\n\n")

    file.write("Dataset rows: " + str(X_all.shape[0]) + "\n")
    file.write("Feature columns: " + str(X_all.shape[1]) + "\n\n")

    file.write("K-Means\n")
    file.write("-------\n")
    file.write("Number of clusters: 2\n")
    file.write("Adjusted Rand Index: " + str(round(adjusted_rand, 4)) + "\n")
    file.write("Normalized Mutual Information: " + str(round(normalized_mutual_info, 4)) + "\n")
    file.write("Cluster counts:\n")
    file.write(str(cluster_counts) + "\n\n")

    file.write("Isolation Forest\n")
    file.write("----------------\n")
    file.write("Contamination setting: 0.10\n")
    file.write("Anomaly flag counts:\n")
    file.write(str(pd.Series(isolation_anomaly_flag).value_counts()) + "\n\n")

    file.write("Notes\n")
    file.write("-----\n")
    file.write("The target label was not used to train the unsupervised models.\n")
    file.write("Known labels were used only after training to interpret results.\n")

print("Saved:", summary_file)
```

Expected result:

```text
Saved: unsupervised\lab09_unsupervised_summary.txt
```

---

## Task 38 - Create Lab 09 Metadata

Run this code:

```python
metadata_file = os.path.join(
    unsupervised_folder,
    "lab09_metadata.txt"
)

with open(metadata_file, "w") as file:
    file.write("Lab 09 - Unsupervised Learning for Cybersecurity Anomaly Detection\n")
    file.write("Rows: " + str(X_all.shape[0]) + "\n")
    file.write("Feature columns: " + str(X_all.shape[1]) + "\n")
    file.write("K-Means clusters: 2\n")
    file.write("K-Means random_state: 42\n")
    file.write("K-Means n_init: 10\n")
    file.write("PCA components: 2\n")
    file.write("Isolation Forest n_estimators: 100\n")
    file.write("Isolation Forest contamination: 0.10\n")
    file.write("Isolation Forest random_state: 42\n")
    file.write("Target used during training: False\n")
    file.write("Known target used for post-training interpretation: True\n")
    file.write("Adjusted Rand Index: " + str(round(adjusted_rand, 4)) + "\n")
    file.write("Normalized Mutual Information: " + str(round(normalized_mutual_info, 4)) + "\n")

print("Saved:", metadata_file)
```

Expected result:

```text
Saved: unsupervised\lab09_metadata.txt
```

---

## Task 39 - List Unsupervised Output Files

Run this code:

```python
print("Files in unsupervised folder:")

for file_name in os.listdir(unsupervised_folder):
    print("-", file_name)
```

Expected result:

```text
The unsupervised output files should display.
```

You should see files such as:

```text
kmeans_cluster_assignments.csv
kmeans_cluster_summary.csv
kmeans_cluster_vs_target.csv
pca_projection.csv
kmeans_pca_clusters.png
isolation_forest_results.csv
isolation_forest_summary.csv
isolation_forest_vs_target.csv
isolation_forest_pca_anomalies.png
top_anomaly_examples.csv
lab09_unsupervised_summary.txt
lab09_metadata.txt
```

---

## Task 40 - Validate Your Work

Run this final validation cell:

```python
required_unsupervised_files = [
    "kmeans_cluster_assignments.csv",
    "kmeans_cluster_summary.csv",
    "kmeans_cluster_vs_target.csv",
    "pca_projection.csv",
    "kmeans_pca_clusters.png",
    "isolation_forest_results.csv",
    "isolation_forest_summary.csv",
    "isolation_forest_vs_target.csv",
    "isolation_forest_pca_anomalies.png",
    "top_anomaly_examples.csv",
    "lab09_unsupervised_summary.txt",
    "lab09_metadata.txt"
]

for file_name in required_unsupervised_files:
    file_path = os.path.join(unsupervised_folder, file_name)
    assert os.path.exists(file_path), f"Missing file: {file_name}"

assert X_all.shape == (11055, 30), "Combined dataset should have 11,055 rows and 30 columns."
assert X_all_scaled.shape == (11055, 30), "Combined scaled dataset should have 11,055 rows and 30 columns."
assert len(y_all) == 11055, "Combined target should have 11,055 values."

assert len(kmeans_clusters) == 11055, "K-Means should assign a cluster to every record."
assert set(pd.Series(kmeans_clusters).unique()).issubset({0, 1}), "K-Means should create clusters 0 and 1."

assert len(isolation_anomaly_flag) == 11055, "Isolation Forest should assign a flag to every record."
assert set(pd.Series(isolation_anomaly_flag).unique()).issubset({0, 1}), "Anomaly flags should be 0 or 1."

assert pca_df.shape[0] == 11055, "PCA table should have 11,055 rows."
assert "PC1" in pca_df.columns, "PCA table should contain PC1."
assert "PC2" in pca_df.columns, "PCA table should contain PC2."

print("Validation passed. Lab 09 is complete.")
```

Expected result:

```text
Validation passed. Lab 09 is complete.
```

---

## Task 41 - Save Your Notebook

1. Select **File**.
2. Select **Save and Checkpoint**.

Confirm the notebook is named:

```text
Lab09-Unsupervised-Anomaly-Detection.ipynb
```

---

## Knowledge Check

Answer the following questions:

1. What is the difference between supervised and unsupervised learning?
2. Did K-Means use the target label during training?
3. What does K-Means clustering try to do?
4. Why did this lab use `n_clusters=2`?
5. What is PCA used for in this lab?
6. What does Isolation Forest try to detect?
7. What does `contamination=0.10` mean?
8. Why did the lab compare clusters and anomaly flags with known target labels after training?
9. What is the difference between a cluster and an anomaly?
10. How could unsupervised learning help cybersecurity analysts when labels are not available?

---

## Troubleshooting

### Problem: Processed Folder Is Missing

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
../Lab08/processed
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

### Problem: K-Means Results Do Not Match the Target Labels

This is normal.

K-Means is unsupervised. It does not know the real target labels.

The purpose is to find natural groupings in the feature data, not to directly copy the target labels.

---

### Problem: PCA Plot Is Hard to Interpret

PCA reduces 30 features down to 2 dimensions.

Some information is lost during this reduction.

The plot is useful for visual exploration, but it should not be treated as a full explanation of the model.

---

### Problem: Isolation Forest Flags Too Many or Too Few Anomalies

The number of anomalies is controlled by:

```python
contamination=0.10
```

Try changing it to:

```python
contamination=0.05
```

or:

```python
contamination=0.15
```

Then rerun the Isolation Forest tasks and compare results.

---

### Problem: Validation Fails Because Shape Is Different

Run:

```python
print(X_all.shape)
print(X_all_scaled.shape)
print(y_all.shape)
```

Expected values are:

```text
X_all: (11055, 30)
X_all_scaled: (11055, 30)
y_all: (11055,)
```

If the values are different, return to Lab 03 and recreate the processed files using the original dataset.

---

## Summary

In this lab, you used unsupervised learning for cybersecurity analysis.

You completed the following tasks:

- Loaded the processed cybersecurity dataset.
- Combined training and testing features for unsupervised learning.
- Trained a K-Means clustering model.
- Compared clusters with known labels after training.
- Created a K-Means cluster summary.
- Used PCA to visualize clusters.
- Trained an Isolation Forest anomaly detection model.
- Created anomaly scores and anomaly flags.
- Compared anomaly results with known labels after training.
- Visualized anomalies using PCA.
- Saved top anomaly examples.
- Created unsupervised learning summary and metadata files.
- Validated all required Lab 09 outputs.

In Lab 10, you can complete a final challenge that brings together the full workflow:

```text
Load data → explore data → prepare data → train model → evaluate model → tune model → interpret model → apply unsupervised anomaly detection
```
