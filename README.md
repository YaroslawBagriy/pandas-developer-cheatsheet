# Pandas Developer Cheat Sheet

A practical Pandas cheat sheet for Python data analysis, cleaning, grouping, and exporting data.

---

## Table of Contents

- [Import Pandas](#import-pandas)
- [Creating DataFrames](#creating-dataframes)
- [Reading Data](#reading-data)
- [Inspecting Data](#inspecting-data)
- [Selecting Columns](#selecting-columns)
- [Selecting Rows](#selecting-rows)
- [Filtering Data](#filtering-data)
- [Adding and Updating Columns](#adding-and-updating-columns)
- [Sorting](#sorting)
- [Missing Values](#missing-values)
- [Duplicates](#duplicates)
- [String Operations](#string-operations)
- [Date and Time](#date-and-time)
- [Grouping and Aggregation](#grouping-and-aggregation)
- [Merging and Joining](#merging-and-joining)
- [Pivot Tables](#pivot-tables)
- [Apply and Map](#apply-and-map)
- [Exporting Data](#exporting-data)
- [Common Patterns](#common-patterns)
- [Common Gotchas](#common-gotchas)

---

## Import Pandas

```python
import pandas as pd
```

---

## Creating DataFrames

### Create a DataFrame from a dictionary

```python
df = pd.DataFrame({
    "name": ["Alice", "Bob", "Charlie"],
    "age": [25, 30, 35],
    "city": ["Minneapolis", "Chicago", "New York"]
})
```

### Create a DataFrame from a list of dictionaries

```python
df = pd.DataFrame([
    {"name": "Alice", "age": 25},
    {"name": "Bob", "age": 30},
    {"name": "Charlie", "age": 35}
])
```

### Create a Series

```python
s = pd.Series([10, 20, 30, 40])
```

---

## Reading Data

### Read CSV

```python
df = pd.read_csv("data.csv")
```

### Read Excel

```python
df = pd.read_excel("data.xlsx")
```

### Read JSON

```python
df = pd.read_json("data.json")
```

### Read from a URL

```python
df = pd.read_csv("https://example.com/data.csv")
```

### Read only selected columns

```python
df = pd.read_csv("data.csv", usecols=["name", "age"])
```

### Read and parse dates

```python
df = pd.read_csv("data.csv", parse_dates=["date"])
```

---

## Inspecting Data

### Show first rows

```python
df.head()
```

### Show last rows

```python
df.tail()
```

### Show random rows

```python
df.sample(5)
```

### Show shape

```python
df.shape
```

### Show column names

```python
df.columns
```

### Show data types

```python
df.dtypes
```

### Summary info

```python
df.info()
```

### Summary statistics

```python
df.describe()
```

### Include non-numeric columns in summary

```python
df.describe(include="all")
```

### Count values in a column

```python
df["column_name"].value_counts()
```

### Count missing values

```python
df.isna().sum()
```

---

## Selecting Columns

### Select one column

```python
df["name"]
```

### Select multiple columns

```python
df[["name", "age"]]
```

### Select columns by data type

```python
df.select_dtypes(include="number")
```

```python
df.select_dtypes(include="object")
```

### Drop columns

```python
df = df.drop(columns=["column_to_drop"])
```

---

## Selecting Rows

### Select row by integer position

```python
df.iloc[0]
```

### Select multiple rows by position

```python
df.iloc[0:5]
```

### Select rows and columns by position

```python
df.iloc[0:5, 0:3]
```

### Select row by index label

```python
df.loc[0]
```

### Select rows and columns by labels

```python
df.loc[0:5, ["name", "age"]]
```

---

## Filtering Data

### Filter rows by condition

```python
df[df["age"] > 30]
```

### Multiple conditions with AND

```python
df[(df["age"] > 25) & (df["city"] == "Chicago")]
```

### Multiple conditions with OR

```python
df[(df["city"] == "Chicago") | (df["city"] == "Minneapolis")]
```

### Filter using `isin`

```python
df[df["city"].isin(["Chicago", "Minneapolis"])]
```

### Filter missing values

```python
df[df["age"].isna()]
```

### Filter non-missing values

```python
df[df["age"].notna()]
```

### Query syntax

```python
df.query("age > 30")
```

```python
df.query("age > 25 and city == 'Chicago'")
```

---

## Adding and Updating Columns

### Add a new column

```python
df["age_plus_10"] = df["age"] + 10
```

### Create column using condition

```python
df["age_group"] = df["age"].apply(lambda x: "adult" if x >= 18 else "minor")
```

### Create column with `np.where`

```python
import numpy as np

df["is_adult"] = np.where(df["age"] >= 18, True, False)
```

### Rename columns

```python
df = df.rename(columns={"old_name": "new_name"})
```

### Rename all columns to lowercase

```python
df.columns = df.columns.str.lower()
```

### Replace values

```python
df["city"] = df["city"].replace("NYC", "New York")
```

---

## Sorting

### Sort by one column

```python
df = df.sort_values("age")
```

### Sort descending

```python
df = df.sort_values("age", ascending=False)
```

### Sort by multiple columns

```python
df = df.sort_values(["city", "age"])
```

### Sort by index

```python
df = df.sort_index()
```

---

## Missing Values

### Check missing values

```python
df.isna()
```

### Count missing values by column

```python
df.isna().sum()
```

### Drop rows with missing values

```python
df = df.dropna()
```

### Drop rows where a specific column is missing

```python
df = df.dropna(subset=["age"])
```

### Fill missing values with a constant

```python
df["age"] = df["age"].fillna(0)
```

### Fill missing values with mean

```python
df["age"] = df["age"].fillna(df["age"].mean())
```

### Forward fill

```python
df = df.ffill()
```

### Backward fill

```python
df = df.bfill()
```

---

## Duplicates

### Check duplicate rows

```python
df.duplicated()
```

### Count duplicate rows

```python
df.duplicated().sum()
```

### Drop duplicate rows

```python
df = df.drop_duplicates()
```

### Drop duplicates based on specific columns

```python
df = df.drop_duplicates(subset=["name", "city"])
```

---

## String Operations

### Convert column to lowercase

```python
df["name"] = df["name"].str.lower()
```

### Convert column to uppercase

```python
df["name"] = df["name"].str.upper()
```

### Strip whitespace

```python
df["name"] = df["name"].str.strip()
```

### Check if string contains text

```python
df[df["name"].str.contains("alice", case=False, na=False)]
```

### Replace text

```python
df["name"] = df["name"].str.replace("old", "new", regex=False)
```

### Split strings

```python
df[["first_name", "last_name"]] = df["full_name"].str.split(" ", expand=True)
```

---

## Date and Time

### Convert column to datetime

```python
df["date"] = pd.to_datetime(df["date"])
```

### Extract year

```python
df["year"] = df["date"].dt.year
```

### Extract month

```python
df["month"] = df["date"].dt.month
```

### Extract day

```python
df["day"] = df["date"].dt.day
```

### Extract day name

```python
df["day_name"] = df["date"].dt.day_name()
```

### Filter by date

```python
df[df["date"] >= "2025-01-01"]
```

### Set date as index

```python
df = df.set_index("date")
```

### Resample time series by month

```python
monthly = df.resample("M").sum()
```

---

## Grouping and Aggregation

### Group by one column

```python
df.groupby("city")["age"].mean()
```

### Group by multiple columns

```python
df.groupby(["city", "gender"])["age"].mean()
```

### Multiple aggregations

```python
df.groupby("city").agg({
    "age": ["mean", "min", "max"],
    "salary": "sum"
})
```

### Reset index after groupby

```python
summary = df.groupby("city")["age"].mean().reset_index()
```

### Count rows per group

```python
df.groupby("city").size()
```

---

## Merging and Joining

### Inner join

```python
merged = pd.merge(df1, df2, on="id", how="inner")
```

### Left join

```python
merged = pd.merge(df1, df2, on="id", how="left")
```

### Right join

```python
merged = pd.merge(df1, df2, on="id", how="right")
```

### Outer join

```python
merged = pd.merge(df1, df2, on="id", how="outer")
```

### Join on different column names

```python
merged = pd.merge(
    df1,
    df2,
    left_on="customer_id",
    right_on="id",
    how="left"
)
```

### Concatenate rows

```python
combined = pd.concat([df1, df2], axis=0)
```

### Concatenate columns

```python
combined = pd.concat([df1, df2], axis=1)
```

---

## Pivot Tables

### Basic pivot table

```python
pivot = pd.pivot_table(
    df,
    values="sales",
    index="region",
    columns="product",
    aggfunc="sum"
)
```

### Pivot with multiple aggregations

```python
pivot = pd.pivot_table(
    df,
    values="sales",
    index="region",
    aggfunc=["sum", "mean"]
)
```

---

## Apply and Map

### Apply function to a column

```python
df["age_group"] = df["age"].apply(lambda x: "adult" if x >= 18 else "minor")
```

### Map values

```python
df["gender_label"] = df["gender"].map({
    "M": "Male",
    "F": "Female"
})
```

### Apply function across rows

```python
df["full_name"] = df.apply(
    lambda row: row["first_name"] + " " + row["last_name"],
    axis=1
)
```

---

## Exporting Data

### Export to CSV

```python
df.to_csv("output.csv", index=False)
```

### Export to Excel

```python
df.to_excel("output.xlsx", index=False)
```

### Export to JSON

```python
df.to_json("output.json", orient="records")
```

### Export to Parquet

```python
df.to_parquet("output.parquet", index=False)
```

---

## Common Patterns

### Clean column names

```python
df.columns = (
    df.columns
    .str.strip()
    .str.lower()
    .str.replace(" ", "_")
)
```

### Convert column to numeric

```python
df["amount"] = pd.to_numeric(df["amount"], errors="coerce")
```

### Convert percentage strings to decimals

```python
df["percent"] = df["percent"].str.replace("%", "", regex=False).astype(float) / 100
```

### Create a binary target column

```python
df["target"] = np.where(df["status"] == "yes", 1, 0)
```

### Get top N rows by column

```python
df.nlargest(10, "sales")
```

### Get bottom N rows by column

```python
df.nsmallest(10, "sales")
```

### Random sample

```python
sample = df.sample(100, random_state=42)
```

### Train/test split manually

```python
train = df.sample(frac=0.8, random_state=42)
test = df.drop(train.index)
```

### Move a column to the front

```python
target = df.pop("target")
df.insert(0, "target", target)
```

---

## Common Gotchas

### Use `&` and `|` for multiple conditions

Correct:

```python
df[(df["age"] > 25) & (df["city"] == "Chicago")]
```

Incorrect:

```python
df[df["age"] > 25 and df["city"] == "Chicago"]
```

### Use parentheses around each condition

Correct:

```python
df[(df["age"] > 25) & (df["salary"] > 50000)]
```

Incorrect:

```python
df[df["age"] > 25 & df["salary"] > 50000]
```

### Avoid chained assignment

Risky:

```python
df[df["age"] > 30]["city"] = "Unknown"
```

Better:

```python
df.loc[df["age"] > 30, "city"] = "Unknown"
```

### `inplace=True` is often unnecessary

Instead of:

```python
df.drop(columns=["col"], inplace=True)
```

Prefer:

```python
df = df.drop(columns=["col"])
```

### Always check shape after major transformations

```python
df.shape
```

### Always inspect missing values before modeling

```python
df.isna().sum()
```
