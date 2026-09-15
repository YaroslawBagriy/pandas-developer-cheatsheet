# Pandas Developer Cheat Sheet

A practical, developer-focused Pandas reference for Python data analysis, cleaning, transformation, joins, time series, feature engineering, and production data workflows.

This cheat sheet is designed to work both as a quick lookup and as lightweight documentation. Examples favor clear, idiomatic Pandas and modern APIs.

> **Convention:** Pandas is imported as `pd` and NumPy as `np` throughout this guide.

---

# Table of Contents

- [Installation and Import](#installation-and-import)
- [Core Objects: Series and DataFrame](#core-objects-series-and-dataframe)
- [Creating Series and DataFrames](#creating-series-and-dataframes)
- [Reading Data](#reading-data)
- [Inspecting Data](#inspecting-data)
- [Data Types](#data-types)
- [Nullable Data Types](#nullable-data-types)
- [Selecting Columns](#selecting-columns)
- [Selecting Rows](#selecting-rows)
- [Indexes](#indexes)
- [MultiIndex](#multiindex)
- [Filtering Data](#filtering-data)
- [Adding and Updating Columns](#adding-and-updating-columns)
- [Sorting](#sorting)
- [Missing Values](#missing-values)
- [Duplicates](#duplicates)
- [String Operations](#string-operations)
- [Categorical Data](#categorical-data)
- [Date and Time](#date-and-time)
- [Datetime Parsing](#datetime-parsing)
- [Datetime Types and Representations](#datetime-types-and-representations)
- [Datetime Components](#datetime-components)
- [Datetime Formatting](#datetime-formatting)
- [Date-Only and Time-Only Values](#date-only-and-time-only-values)
- [Time Zones](#time-zones)
- [Daylight Saving Time](#daylight-saving-time)
- [Unix Epoch Timestamps](#unix-epoch-timestamps)
- [Timedeltas and Durations](#timedeltas-and-durations)
- [Date Ranges and Frequencies](#date-ranges-and-frequencies)
- [Offsets and Business Dates](#offsets-and-business-dates)
- [Periods and PeriodIndex](#periods-and-periodindex)
- [DatetimeIndex](#datetimeindex)
- [Filtering by Date and Time](#filtering-by-date-and-time)
- [Resampling Time Series](#resampling-time-series)
- [Rolling, Expanding, and EWM Windows](#rolling-expanding-and-ewm-windows)
- [Datetime Missing Values and NaT](#datetime-missing-values-and-nat)
- [Datetime I/O](#datetime-io)
- [Datetime Pitfalls](#datetime-pitfalls)
- [Grouping and Aggregation](#grouping-and-aggregation)
- [Transform and Filter by Group](#transform-and-filter-by-group)
- [Merging and Joining](#merging-and-joining)
- [Concatenation](#concatenation)
- [Reshaping](#reshaping)
- [Pivot Tables](#pivot-tables)
- [Apply, Map, and Vectorized Alternatives](#apply-map-and-vectorized-alternatives)
- [Window Functions](#window-functions)
- [Exporting Data](#exporting-data)
- [Common Data Cleaning Patterns](#common-data-cleaning-patterns)
- [Common Analysis Patterns](#common-analysis-patterns)
- [Performance and Memory](#performance-and-memory)
- [Copying, Views, and Assignment](#copying-views-and-assignment)
- [Debugging and Validation](#debugging-and-validation)
- [Common Gotchas](#common-gotchas)
- [Quick Reference](#quick-reference)
- [Pandas Function and Method Reference](#pandas-function-and-method-reference)
- [Official Documentation](#official-documentation)

---

# Installation and Import

Install Pandas:

```bash
python -m pip install pandas
```

Import using the standard alias:

```python
import pandas as pd
```

NumPy is frequently used alongside Pandas:

```python
import numpy as np
```

Check the installed version:

```python
print(pd.__version__)
```

## Why Pandas?

Pandas provides labeled data structures and high-level tools for:

- tabular data
- time-series data
- missing values
- joins and merges
- grouping and aggregation
- reshaping
- string processing
- data cleaning
- type conversion
- input/output
- feature engineering

### Documentation

- Pandas documentation: https://pandas.pydata.org/docs/
- User guide: https://pandas.pydata.org/docs/user_guide/
- API reference: https://pandas.pydata.org/docs/reference/

---

# Core Objects: Series and DataFrame

Pandas has two primary data structures.

## Series

A `Series` is a one-dimensional labeled array.

```python
s = pd.Series([10, 20, 30])

print(s)
```

Conceptually:

```text
index    value
0        10
1        20
2        30
```

Custom index:

```python
s = pd.Series(
    [10, 20, 30],
    index=["a", "b", "c"]
)
```

Access by label:

```python
s.loc["b"]
# 20
```

Access by position:

```python
s.iloc[1]
# 20
```

## DataFrame

A `DataFrame` is a two-dimensional labeled table.

```python
df = pd.DataFrame({
    "name": ["Alice", "Bob", "Charlie"],
    "age": [25, 30, 35],
    "city": ["Minneapolis", "Chicago", "New York"]
})
```

Conceptually:

```text
   name      age  city
0  Alice      25  Minneapolis
1  Bob        30  Chicago
2  Charlie    35  New York
```

Each DataFrame column is a `Series`:

```python
type(df["age"])
# pandas.Series
```

### Documentation

- Series: https://pandas.pydata.org/docs/reference/api/pandas.Series.html
- DataFrame: https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.html

---

# Creating Series and DataFrames

## Create a Series from a list

```python
s = pd.Series([10, 20, 30, 40])
```

## Series with custom index

```python
s = pd.Series(
    [100, 200, 300],
    index=["a", "b", "c"],
    name="sales"
)
```

## Create a DataFrame from a dictionary

```python
df = pd.DataFrame({
    "name": ["Alice", "Bob", "Charlie"],
    "age": [25, 30, 35],
    "city": ["Minneapolis", "Chicago", "New York"]
})
```

## Create from a list of dictionaries

```python
df = pd.DataFrame([
    {"name": "Alice", "age": 25},
    {"name": "Bob", "age": 30},
    {"name": "Charlie", "age": 35}
])
```

Missing keys become missing values:

```python
df = pd.DataFrame([
    {"name": "Alice", "age": 25},
    {"name": "Bob"}
])
```

## Create from records

```python
records = [
    ("Alice", 25),
    ("Bob", 30)
]

df = pd.DataFrame(
    records,
    columns=["name", "age"]
)
```

## Create from NumPy array

```python
arr = np.array([
    [1, 2],
    [3, 4]
])

df = pd.DataFrame(
    arr,
    columns=["a", "b"]
)
```

## Create empty DataFrame

```python
df = pd.DataFrame(
    columns=["name", "age", "city"]
)
```

## Create from dictionary of Series

```python
df = pd.DataFrame({
    "a": pd.Series([1, 2, 3]),
    "b": pd.Series([10, 20, 30])
})
```

### Documentation

- DataFrame constructor: https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.html
- Series constructor: https://pandas.pydata.org/docs/reference/api/pandas.Series.html

---

# Reading Data

Pandas supports many data formats.

## Read CSV

```python
df = pd.read_csv("data.csv")
```

## Selected columns only

```python
df = pd.read_csv(
    "data.csv",
    usecols=["name", "age"]
)
```

## Specify dtypes

```python
df = pd.read_csv(
    "data.csv",
    dtype={
        "user_id": "string",
        "age": "Int64"
    }
)
```

## Parse dates while reading

```python
df = pd.read_csv(
    "data.csv",
    parse_dates=["created_at"]
)
```

For complex date parsing, it is often clearer to read first and call `pd.to_datetime()` explicitly.

## Custom missing-value markers

```python
df = pd.read_csv(
    "data.csv",
    na_values=["NA", "N/A", "NULL", ""]
)
```

## Read only first N rows

```python
df = pd.read_csv(
    "data.csv",
    nrows=1000
)
```

## Read large CSV in chunks

```python
for chunk in pd.read_csv(
    "large.csv",
    chunksize=100_000
):
    process(chunk)
```

## Read Excel

```python
df = pd.read_excel("data.xlsx")
```

Specific sheet:

```python
df = pd.read_excel(
    "data.xlsx",
    sheet_name="Sales"
)
```

All sheets:

```python
sheets = pd.read_excel(
    "data.xlsx",
    sheet_name=None
)
```

Returns a dictionary of DataFrames.

## Read JSON

```python
df = pd.read_json("data.json")
```

Line-delimited JSON:

```python
df = pd.read_json(
    "data.jsonl",
    lines=True
)
```

## Read Parquet

```python
df = pd.read_parquet("data.parquet")
```

Parquet is typically preferable to CSV for typed analytical data because it preserves schemas better and is more compact.

## Read SQL query

```python
query = """
SELECT
    user_id,
    created_at,
    amount
FROM transactions
"""

df = pd.read_sql_query(
    query,
    connection
)
```

## Read HTML tables

```python
tables = pd.read_html(
    "https://example.com/page"
)
```

## Read clipboard

```python
df = pd.read_clipboard()
```

### Documentation

- I/O tools: https://pandas.pydata.org/docs/user_guide/io.html
- `read_csv`: https://pandas.pydata.org/docs/reference/api/pandas.read_csv.html
- `read_excel`: https://pandas.pydata.org/docs/reference/api/pandas.read_excel.html
- `read_parquet`: https://pandas.pydata.org/docs/reference/api/pandas.read_parquet.html

---

# Inspecting Data

## First rows

```python
df.head()
```

```python
df.head(10)
```

## Last rows

```python
df.tail()
```

## Random rows

```python
df.sample(5)
```

Reproducible sample:

```python
df.sample(
    5,
    random_state=42
)
```

## Shape

```python
df.shape
# (rows, columns)
```

## Row count

```python
len(df)
```

or:

```python
df.shape[0]
```

## Column count

```python
df.shape[1]
```

## Column names

```python
df.columns
```

As list:

```python
df.columns.tolist()
```

## Index

```python
df.index
```

## Data types

```python
df.dtypes
```

## Detailed summary

```python
df.info()
```

Useful for:

- row count
- column names
- non-null counts
- dtypes
- memory usage

## Memory usage

```python
df.memory_usage()
```

Deep inspection for strings/objects:

```python
df.memory_usage(
    deep=True
)
```

Total bytes:

```python
df.memory_usage(
    deep=True
).sum()
```

## Numeric summary statistics

```python
df.describe()
```

## Include all columns

```python
df.describe(
    include="all"
)
```

## Count unique values

```python
df["city"].nunique()
```

## Count values

```python
df["city"].value_counts()
```

Include missing:

```python
df["city"].value_counts(
    dropna=False
)
```

Normalize to proportions:

```python
df["city"].value_counts(
    normalize=True
)
```

## Missing-value counts

```python
df.isna().sum()
```

Percent missing:

```python
df.isna().mean() * 100
```

## Unique values

```python
df["city"].unique()
```

## Correlations

```python
df.corr(
    numeric_only=True
)
```

### Documentation

- `DataFrame.info`: https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.info.html
- `DataFrame.describe`: https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.describe.html
- `Series.value_counts`: https://pandas.pydata.org/docs/reference/api/pandas.Series.value_counts.html

---

# Data Types

Pandas builds on NumPy dtypes and adds extension dtypes.

## Inspect dtypes

```python
df.dtypes
```

## Common dtypes

Typical Pandas/NumPy-backed types include:

```text
int64
float64
bool
object
datetime64[ns]
timedelta64[ns]
```

Pandas extension types include:

```text
Int64
Float64
boolean
string
category
datetime64[ns, UTC]
```

## Convert with `astype`

```python
df["age"] = df["age"].astype("int64")
```

String dtype:

```python
df["name"] = df["name"].astype("string")
```

Nullable integer:

```python
df["age"] = df["age"].astype("Int64")
```

## Convert numeric values safely

```python
df["amount"] = pd.to_numeric(
    df["amount"],
    errors="coerce"
)
```

Invalid values become `NaN`.

## Convert datetimes

```python
df["created_at"] = pd.to_datetime(
    df["created_at"],
    errors="coerce"
)
```

## Convert timedeltas

```python
df["duration"] = pd.to_timedelta(
    df["duration"],
    errors="coerce"
)
```

## Infer better dtypes

```python
df = df.convert_dtypes()
```

This often converts:

- `object` strings → `string`
- integer-with-missing → `Int64`
- boolean-with-missing → `boolean`

## Select columns by dtype

Numeric:

```python
df.select_dtypes(
    include="number"
)
```

Strings:

```python
df.select_dtypes(
    include="string"
)
```

Datetimes:

```python
df.select_dtypes(
    include=["datetime", "datetimetz"]
)
```

Exclude object:

```python
df.select_dtypes(
    exclude="object"
)
```

### Documentation

- Dtypes: https://pandas.pydata.org/docs/user_guide/basics.html#dtypes
- `astype`: https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.astype.html
- `to_numeric`: https://pandas.pydata.org/docs/reference/api/pandas.to_numeric.html
- `convert_dtypes`: https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.convert_dtypes.html

---

# Nullable Data Types

Traditional NumPy integer and boolean dtypes cannot represent missing values directly.

Pandas provides nullable extension dtypes.

## Nullable integer

```python
s = pd.Series(
    [1, 2, None],
    dtype="Int64"
)
```

Notice capital `I`.

## Nullable floating point

```python
s = pd.Series(
    [1.5, None, 3.2],
    dtype="Float64"
)
```

## Nullable boolean

```python
s = pd.Series(
    [True, False, None],
    dtype="boolean"
)
```

## String dtype

```python
s = pd.Series(
    ["a", "b", None],
    dtype="string"
)
```

## Missing scalar

Pandas' general-purpose missing scalar is:

```python
pd.NA
```

Example:

```python
s = pd.Series(
    [1, pd.NA, 3],
    dtype="Int64"
)
```

## Why use nullable dtypes?

They preserve logical types even when values are missing.

Instead of:

```text
integer + missing -> float64
```

you can keep:

```text
Int64
```

### Documentation

- Nullable integer: https://pandas.pydata.org/docs/user_guide/integer_na.html
- Nullable boolean: https://pandas.pydata.org/docs/user_guide/boolean.html
- Text data: https://pandas.pydata.org/docs/user_guide/text.html

---

# Selecting Columns

## One column

```python
df["name"]
```

Returns a Series.

## Multiple columns

```python
df[
    ["name", "age"]
]
```

Returns a DataFrame.

## Attribute syntax

```python
df.name
```

Avoid relying on attribute access in production code because it fails for:

- columns with spaces
- names colliding with DataFrame methods
- dynamically chosen columns

Prefer:

```python
df["name"]
```

## Select columns by dtype

```python
df.select_dtypes(
    include="number"
)
```

## Select columns by name pattern

```python
df.filter(
    like="sales"
)
```

Regex:

```python
df.filter(
    regex=r"^sales_"
)
```

## Drop columns

```python
df = df.drop(
    columns=["temporary_column"]
)
```

Multiple:

```python
df = df.drop(
    columns=["a", "b"]
)
```

## Reorder columns

```python
df = df[
    ["id", "name", "age", "city"]
]
```

### Documentation

- Indexing and selecting data: https://pandas.pydata.org/docs/user_guide/indexing.html
- `DataFrame.filter`: https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.filter.html

---

# Selecting Rows

Pandas provides label-based and position-based indexing.

## `iloc`: integer position

First row:

```python
df.iloc[0]
```

First five rows:

```python
df.iloc[:5]
```

Rows and columns by position:

```python
df.iloc[
    0:5,
    0:3
]
```

Single cell:

```python
df.iloc[0, 2]
```

## `loc`: labels

Row with index label `100`:

```python
df.loc[100]
```

Rows and columns:

```python
df.loc[
    100:105,
    ["name", "age"]
]
```

> Unlike normal Python slicing, `.loc[start:end]` is generally inclusive of the end label.

## Boolean mask with `loc`

```python
mask = df["age"] > 30

df.loc[
    mask,
    ["name", "age"]
]
```

## Fast scalar access

Label-based:

```python
df.at[
    row_label,
    "column"
]
```

Position-based:

```python
df.iat[
    row_position,
    column_position
]
```

### Documentation

- Indexing: https://pandas.pydata.org/docs/user_guide/indexing.html
- `loc`: https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.loc.html
- `iloc`: https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.iloc.html

---

# Indexes

An index labels rows.

## Inspect index

```python
df.index
```

## Set column as index

```python
df = df.set_index(
    "user_id"
)
```

## Reset index

```python
df = df.reset_index()
```

Drop old index completely:

```python
df = df.reset_index(
    drop=True
)
```

## Name the index

```python
df.index.name = "row_id"
```

## Check uniqueness

```python
df.index.is_unique
```

## Check monotonic ordering

```python
df.index.is_monotonic_increasing
```

## Find duplicated index labels

```python
df.index.duplicated()
```

## Sort index

```python
df = df.sort_index()
```

## Reindex

```python
df = df.reindex(
    [100, 200, 300]
)
```

Missing labels create missing rows.

### Documentation

- Index objects: https://pandas.pydata.org/docs/reference/indexing.html
- `set_index`: https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.set_index.html
- `reset_index`: https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.reset_index.html

---

# MultiIndex

A `MultiIndex` provides hierarchical row or column labels.

## Create from columns

```python
df = df.set_index(
    ["region", "store"]
)
```

## Select a top-level group

```python
df.loc["Midwest"]
```

## Select an exact tuple

```python
df.loc[
    ("Midwest", "Store_A")
]
```

## Sort MultiIndex

```python
df = df.sort_index()
```

## Reset MultiIndex

```python
df = df.reset_index()
```

## Create explicitly

```python
index = pd.MultiIndex.from_product(
    [
        ["A", "B"],
        [1, 2]
    ],
    names=["group", "id"]
)
```

## Cross-section

```python
df.xs(
    "Store_A",
    level="store"
)
```

### Documentation

- MultiIndex / advanced indexing: https://pandas.pydata.org/docs/user_guide/advanced.html

---

# Filtering Data

## Basic condition

```python
df[
    df["age"] > 30
]
```

## AND

```python
df[
    (df["age"] > 25)
    & (df["city"] == "Chicago")
]
```

## OR

```python
df[
    (df["city"] == "Chicago")
    | (df["city"] == "Minneapolis")
]
```

## NOT

```python
df[
    ~(df["city"] == "Chicago")
]
```

## Membership with `isin`

```python
df[
    df["city"].isin(
        ["Chicago", "Minneapolis"]
    )
]
```

Not in:

```python
df[
    ~df["city"].isin(
        ["Chicago", "Minneapolis"]
    )
]
```

## Range filtering

```python
df[
    df["age"].between(
        25,
        40,
        inclusive="both"
    )
]
```

## Missing values

```python
df[
    df["age"].isna()
]
```

Non-missing:

```python
df[
    df["age"].notna()
]
```

## String contains

```python
df[
    df["name"].str.contains(
        "alice",
        case=False,
        na=False
    )
]
```

## Query syntax

```python
df.query(
    "age > 30"
)
```

```python
df.query(
    "age > 25 and city == 'Chicago'"
)
```

Reference Python variable:

```python
minimum_age = 30

df.query(
    "age >= @minimum_age"
)
```

### Documentation

- Boolean indexing: https://pandas.pydata.org/docs/user_guide/indexing.html#boolean-indexing
- `query`: https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.query.html
- `between`: https://pandas.pydata.org/docs/reference/api/pandas.Series.between.html

---

# Adding and Updating Columns

## Add a new column

```python
df["age_plus_10"] = (
    df["age"] + 10
)
```

## Assign a scalar

```python
df["source"] = "web"
```

## Conditional column

```python
df["is_adult"] = (
    df["age"] >= 18
)
```

## `np.where`

```python
df["age_group"] = np.where(
    df["age"] >= 18,
    "adult",
    "minor"
)
```

## `np.select` for multiple conditions

```python
conditions = [
    df["score"] >= 90,
    df["score"] >= 80,
    df["score"] >= 70
]

choices = [
    "A",
    "B",
    "C"
]

df["grade"] = np.select(
    conditions,
    choices,
    default="D"
)
```

## `assign`

```python
df = df.assign(
    age_plus_10=df["age"] + 10,
    is_adult=df["age"] >= 18
)
```

Useful in method chains.

## Update values using `loc`

```python
df.loc[
    df["age"] > 30,
    "age_group"
] = "30+"
```

## Rename columns

```python
df = df.rename(
    columns={
        "old_name": "new_name"
    }
)
```

## Normalize column names

```python
df.columns = (
    df.columns
    .str.strip()
    .str.lower()
    .str.replace(
        " ",
        "_",
        regex=False
    )
)
```

## Insert at a position

```python
df.insert(
    0,
    "id",
    range(len(df))
)
```

## Delete a column

```python
del df["temporary"]
```

or:

```python
column = df.pop(
    "temporary"
)
```

### Documentation

- `assign`: https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.assign.html
- `rename`: https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.rename.html

---

# Sorting

## Sort one column

```python
df = df.sort_values(
    "age"
)
```

## Descending

```python
df = df.sort_values(
    "age",
    ascending=False
)
```

## Multiple columns

```python
df = df.sort_values(
    ["city", "age"],
    ascending=[True, False]
)
```

## Missing values first

```python
df = df.sort_values(
    "age",
    na_position="first"
)
```

## Sort index

```python
df = df.sort_index()
```

## Top N

```python
df.nlargest(
    10,
    "sales"
)
```

## Bottom N

```python
df.nsmallest(
    10,
    "sales"
)
```

### Documentation

- `sort_values`: https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.sort_values.html
- `nlargest`: https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.nlargest.html

---

# Missing Values

Pandas may represent missing values using:

- `np.nan`
- `pd.NA`
- `pd.NaT`
- `None`

depending on dtype.

## Check missing

```python
df.isna()
```

Equivalent:

```python
df.isnull()
```

## Count missing per column

```python
df.isna().sum()
```

## Percent missing

```python
df.isna().mean() * 100
```

## Rows with any missing value

```python
df[
    df.isna().any(axis=1)
]
```

## Rows with all missing values

```python
df[
    df.isna().all(axis=1)
]
```

## Drop rows with missing values

```python
df = df.dropna()
```

## Drop rows where selected columns are missing

```python
df = df.dropna(
    subset=["age", "city"]
)
```

## Require minimum number of non-null values

```python
df = df.dropna(
    thresh=3
)
```

## Fill constant

```python
df["age"] = df["age"].fillna(
    0
)
```

## Fill with statistic

```python
df["age"] = df["age"].fillna(
    df["age"].median()
)
```

## Forward fill

```python
df = df.ffill()
```

## Backward fill

```python
df = df.bfill()
```

## Interpolation

```python
df["value"] = (
    df["value"]
    .interpolate()
)
```

## Replace sentinel values

```python
df["score"] = df["score"].replace(
    -999,
    pd.NA
)
```

### Documentation

- Missing data: https://pandas.pydata.org/docs/user_guide/missing_data.html
- `dropna`: https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.dropna.html
- `fillna`: https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.fillna.html
- `interpolate`: https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.interpolate.html

---

# Duplicates

## Identify duplicate rows

```python
df.duplicated()
```

## Count duplicate rows

```python
df.duplicated().sum()
```

## Show duplicates

```python
df[
    df.duplicated(
        keep=False
    )
]
```

## Duplicate based on selected columns

```python
df.duplicated(
    subset=["name", "city"]
)
```

## Drop duplicates

```python
df = df.drop_duplicates()
```

## Keep last occurrence

```python
df = df.drop_duplicates(
    subset=["user_id"],
    keep="last"
)
```

## Keep no duplicated values

```python
df = df.drop_duplicates(
    subset=["user_id"],
    keep=False
)
```

### Documentation

- `duplicated`: https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.duplicated.html
- `drop_duplicates`: https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.drop_duplicates.html

---

# String Operations

The `.str` accessor provides vectorized string operations.

```python
s = df["name"].astype("string")
```

## Lowercase

```python
df["name"] = (
    df["name"]
    .str.lower()
)
```

## Uppercase

```python
df["name"] = (
    df["name"]
    .str.upper()
)
```

## Title case

```python
df["name"] = (
    df["name"]
    .str.title()
)
```

## Strip whitespace

```python
df["name"] = (
    df["name"]
    .str.strip()
)
```

Left/right only:

```python
df["name"].str.lstrip()
df["name"].str.rstrip()
```

## Contains

```python
mask = (
    df["name"]
    .str.contains(
        "alice",
        case=False,
        na=False
    )
)
```

Regex:

```python
mask = (
    df["email"]
    .str.contains(
        r"@example\.com$",
        regex=True,
        na=False
    )
)
```

## Starts/ends with

```python
df["code"].str.startswith(
    "ABC"
)
```

```python
df["file"].str.endswith(
    ".csv"
)
```

## Replace

Literal:

```python
df["name"] = (
    df["name"]
    .str.replace(
        "old",
        "new",
        regex=False
    )
)
```

Regex:

```python
df["phone"] = (
    df["phone"]
    .str.replace(
        r"\D",
        "",
        regex=True
    )
)
```

## Split

```python
df[
    ["first_name", "last_name"]
] = (
    df["full_name"]
    .str.split(
        " ",
        n=1,
        expand=True
    )
)
```

## Extract regex groups

```python
df["domain"] = (
    df["email"]
    .str.extract(
        r"@(.+)$",
        expand=False
    )
)
```

## String length

```python
df["name_length"] = (
    df["name"]
    .str.len()
)
```

## Pad / zero-fill

```python
df["id"] = (
    df["id"]
    .str.zfill(8)
)
```

### Documentation

- Working with text: https://pandas.pydata.org/docs/user_guide/text.html
- String methods: https://pandas.pydata.org/docs/reference/series.html#string-handling

---

# Categorical Data

Categorical dtype is useful for columns containing a limited set of repeated values.

## Convert to category

```python
df["status"] = (
    df["status"]
    .astype("category")
)
```

## Inspect categories

```python
df["status"].cat.categories
```

## Ordered category

```python
priority_type = pd.CategoricalDtype(
    categories=[
        "low",
        "medium",
        "high"
    ],
    ordered=True
)

df["priority"] = (
    df["priority"]
    .astype(priority_type)
)
```

Now comparisons and sorting respect the category order.

## Rename categories

```python
df["status"] = (
    df["status"]
    .cat.rename_categories({
        "A": "Active",
        "I": "Inactive"
    })
)
```

## Add a category

```python
df["status"] = (
    df["status"]
    .cat.add_categories(
        ["Unknown"]
    )
)
```

## Category codes

```python
df["status"].cat.codes
```

Missing values use code `-1`.

## When category is useful

Good candidates:

- states
- status values
- product types
- priority levels
- repeated labels

Potential benefits:

- reduced memory usage
- explicit domain constraints
- ordered semantics

### Documentation

- Categorical data: https://pandas.pydata.org/docs/user_guide/categorical.html

---

# Date and Time

Pandas has extensive date/time support. The main datetime-related types are:

| Concept | Pandas / Python representation |
|---|---|
| Single timestamp | `pd.Timestamp` |
| Datetime Series | `datetime64[ns]` or timezone-aware datetime dtype |
| Datetime index | `pd.DatetimeIndex` |
| Missing datetime | `pd.NaT` |
| Duration | `pd.Timedelta` |
| Duration index | `pd.TimedeltaIndex` |
| Calendar period | `pd.Period` |
| Period index | `pd.PeriodIndex` |
| Date range | `pd.date_range()` |
| Duration range | `pd.timedelta_range()` |
| Period range | `pd.period_range()` |
| Calendar offset | `pd.DateOffset` / offset classes |
| Python date | `datetime.date` |
| Python time | `datetime.time` |
| Python datetime | `datetime.datetime` |

The most important distinction is between:

- **point in time** → Timestamp/datetime
- **duration** → Timedelta
- **calendar span** → Period

### Documentation

- Time series / date functionality: https://pandas.pydata.org/docs/user_guide/timeseries.html
- `Timestamp`: https://pandas.pydata.org/docs/reference/api/pandas.Timestamp.html
- `Timedelta`: https://pandas.pydata.org/docs/reference/api/pandas.Timedelta.html
- `Period`: https://pandas.pydata.org/docs/reference/api/pandas.Period.html

---

# Datetime Parsing

`pd.to_datetime()` is the primary datetime parser.

## Basic conversion

```python
df["timestamp"] = pd.to_datetime(
    df["timestamp"]
)
```

## Convert one value

```python
ts = pd.to_datetime(
    "2026-09-15 14:30:00"
)

print(type(ts))
# pandas.Timestamp
```

## Invalid values

Raise an exception:

```python
pd.to_datetime(
    values,
    errors="raise"
)
```

Convert invalid values to `NaT`:

```python
pd.to_datetime(
    values,
    errors="coerce"
)
```

Example:

```python
s = pd.Series([
    "2026-01-01",
    "not-a-date",
    "2026-03-01"
])

parsed = pd.to_datetime(
    s,
    errors="coerce"
)
```

Result:

```text
2026-01-01
NaT
2026-03-01
```

## Explicit format

Use an explicit format when you know the layout.

```python
df["timestamp"] = pd.to_datetime(
    df["timestamp"],
    format="%Y-%m-%d %H:%M:%S"
)
```

This makes parsing intent explicit.

## Fractional seconds

```python
pd.to_datetime(
    "2026-09-15 14:30:10.123456"
)
```

Format:

```python
pd.to_datetime(
    "2026-09-15 14:30:10.123456",
    format="%Y-%m-%d %H:%M:%S.%f"
)
```

## ISO 8601

Examples:

```text
2026-09-15
2026-09-15T14:30:00
2026-09-15T14:30:00Z
2026-09-15T14:30:00-05:00
```

Parse:

```python
pd.to_datetime(
    "2026-09-15T14:30:00Z"
)
```

For mixed ISO timestamps:

```python
pd.to_datetime(
    values,
    format="ISO8601"
)
```

## Mixed datetime formats

If input strings contain multiple formats:

```python
pd.to_datetime(
    values,
    format="mixed"
)
```

Use carefully: explicit, consistent source formats are preferable.

## Day-first dates

For inputs such as:

```text
15/09/2026
```

use:

```python
pd.to_datetime(
    values,
    dayfirst=True
)
```

## Year-first dates

```python
pd.to_datetime(
    values,
    yearfirst=True
)
```

## UTC while parsing

```python
df["timestamp"] = pd.to_datetime(
    df["timestamp"],
    utc=True
)
```

This is particularly useful when incoming timestamps contain mixed UTC offsets.

## Assemble datetime from columns

```python
df = pd.DataFrame({
    "year": [2026, 2026],
    "month": [9, 10],
    "day": [15, 1],
    "hour": [9, 14]
})

df["timestamp"] = pd.to_datetime(
    df[
        ["year", "month", "day", "hour"]
    ]
)
```

## Parse numeric epochs

```python
pd.to_datetime(
    1_700_000_000,
    unit="s",
    utc=True
)
```

More on epoch units later.

### Common format codes

| Code | Meaning | Example |
|---|---|---|
| `%Y` | 4-digit year | `2026` |
| `%y` | 2-digit year | `26` |
| `%m` | month number | `09` |
| `%b` | abbreviated month | `Sep` |
| `%B` | full month | `September` |
| `%d` | day | `15` |
| `%H` | hour 00-23 | `14` |
| `%I` | hour 01-12 | `02` |
| `%M` | minute | `30` |
| `%S` | second | `45` |
| `%f` | microseconds | `123456` |
| `%p` | AM/PM | `PM` |
| `%a` | abbreviated weekday | `Tue` |
| `%A` | full weekday | `Tuesday` |
| `%j` | day of year | `258` |
| `%z` | UTC offset | `-0500` |
| `%Z` | timezone name | `UTC` |
| `%V` | ISO week | `38` |
| `%G` | ISO week-year | `2026` |
| `%u` | ISO weekday | `2` |

### Documentation

- `to_datetime`: https://pandas.pydata.org/docs/reference/api/pandas.to_datetime.html

---

# Datetime Types and Representations

Not every object that looks like a date is the same type.

## Python `datetime.datetime`

```python
from datetime import datetime

dt = datetime(
    2026,
    9,
    15,
    14,
    30
)
```

## Pandas `Timestamp`

```python
ts = pd.Timestamp(
    "2026-09-15 14:30:00"
)
```

`Timestamp` behaves similarly to Python `datetime` but integrates directly with Pandas/NumPy.

## NumPy `datetime64`

```python
value = np.datetime64(
    "2026-09-15T14:30:00"
)
```

## Datetime Series

```python
s = pd.Series(
    pd.to_datetime([
        "2026-09-15",
        "2026-09-16"
    ])
)
```

Typical dtype:

```text
datetime64[ns]
```

## Timezone-aware dtype

```python
s = pd.Series(
    pd.to_datetime(
        [
            "2026-09-15T14:30:00Z",
            "2026-09-16T14:30:00Z"
        ],
        utc=True
    )
)
```

Typical dtype:

```text
datetime64[ns, UTC]
```

## `DatetimeIndex`

```python
idx = pd.DatetimeIndex([
    "2026-09-15",
    "2026-09-16"
])
```

## Naive datetime

A naive datetime has no timezone metadata.

```python
ts = pd.Timestamp(
    "2026-09-15 14:30"
)

print(ts.tz)
# None
```

## Timezone-aware datetime

```python
ts = pd.Timestamp(
    "2026-09-15 14:30",
    tz="America/Chicago"
)
```

## Precision / resolution

Pandas datetime values are backed by `datetime64` with a time unit.

Common units include:

```text
s   seconds
ms  milliseconds
us  microseconds
ns  nanoseconds
```

When dealing with external systems, always know the unit.

### Documentation

- Timestamp: https://pandas.pydata.org/docs/reference/api/pandas.Timestamp.html
- DatetimeIndex: https://pandas.pydata.org/docs/reference/api/pandas.DatetimeIndex.html

---

# Datetime Components

Use the `.dt` accessor on a datetime Series.

```python
df["timestamp"] = pd.to_datetime(
    df["timestamp"]
)
```

## Calendar components

```python
df["year"] = (
    df["timestamp"].dt.year
)

df["month"] = (
    df["timestamp"].dt.month
)

df["day"] = (
    df["timestamp"].dt.day
)
```

## Time components

```python
df["hour"] = (
    df["timestamp"].dt.hour
)

df["minute"] = (
    df["timestamp"].dt.minute
)

df["second"] = (
    df["timestamp"].dt.second
)

df["microsecond"] = (
    df["timestamp"].dt.microsecond
)
```

## Day of week

Monday = `0`.

```python
df["day_of_week"] = (
    df["timestamp"].dt.dayofweek
)
```

Alias:

```python
df["timestamp"].dt.weekday
```

## Day name

```python
df["day_name"] = (
    df["timestamp"].dt.day_name()
)
```

## Month name

```python
df["month_name"] = (
    df["timestamp"].dt.month_name()
)
```

## Day of year

```python
df["day_of_year"] = (
    df["timestamp"].dt.dayofyear
)
```

## Quarter

```python
df["quarter"] = (
    df["timestamp"].dt.quarter
)
```

## Days in month

```python
df["days_in_month"] = (
    df["timestamp"].dt.days_in_month
)
```

## Month start/end checks

```python
df["timestamp"].dt.is_month_start
df["timestamp"].dt.is_month_end
```

## Quarter start/end

```python
df["timestamp"].dt.is_quarter_start
df["timestamp"].dt.is_quarter_end
```

## Year start/end

```python
df["timestamp"].dt.is_year_start
df["timestamp"].dt.is_year_end
```

## Leap year

```python
df["timestamp"].dt.is_leap_year
```

## ISO calendar

```python
iso = (
    df["timestamp"]
    .dt.isocalendar()
)
```

Returns fields such as:

```text
year
week
day
```

Example:

```python
df["iso_week"] = (
    df["timestamp"]
    .dt.isocalendar()
    .week
)
```

### Documentation

- Datetime properties: https://pandas.pydata.org/docs/reference/series.html#datetimelike-properties

---

# Datetime Formatting

Use `.dt.strftime()` to convert datetimes to strings.

```python
df["date_text"] = (
    df["timestamp"]
    .dt.strftime(
        "%Y-%m-%d"
    )
)
```

## Date + time

```python
df["formatted"] = (
    df["timestamp"]
    .dt.strftime(
        "%Y-%m-%d %H:%M:%S"
    )
)
```

## US-style date

```python
df["formatted"] = (
    df["timestamp"]
    .dt.strftime(
        "%m/%d/%Y"
    )
)
```

## Human-readable

```python
df["formatted"] = (
    df["timestamp"]
    .dt.strftime(
        "%B %d, %Y"
    )
)
```

Example:

```text
September 15, 2026
```

## ISO-like output

```python
df["formatted"] = (
    df["timestamp"]
    .dt.strftime(
        "%Y-%m-%dT%H:%M:%S%z"
    )
)
```

## Important: formatting changes the dtype

Before:

```text
datetime64[ns]
```

After `.dt.strftime()`:

```text
object/string-like values
```

Keep a real datetime column for computation. Create formatted text only for presentation/export when needed.

### Documentation

- `Series.dt.strftime`: https://pandas.pydata.org/docs/reference/api/pandas.Series.dt.strftime.html

---

# Date-Only and Time-Only Values

Pandas primarily stores timestamps, not a dedicated native date-only Series dtype.

## Extract Python `date`

```python
df["date_only"] = (
    df["timestamp"].dt.date
)
```

Values are Python `datetime.date` objects.

This commonly results in `object` dtype.

## Preserve vectorized datetime behavior

Often better:

```python
df["date_floor"] = (
    df["timestamp"]
    .dt.normalize()
)
```

This sets the time to midnight while keeping datetime dtype.

Example:

```text
2026-09-15 14:30:00
```

becomes:

```text
2026-09-15 00:00:00
```

## Floor to day

```python
df["day"] = (
    df["timestamp"]
    .dt.floor("D")
)
```

## Extract Python `time`

```python
df["time_only"] = (
    df["timestamp"].dt.time
)
```

Values become Python `datetime.time` objects.

## Extract timezone-aware time

```python
df["time_tz"] = (
    df["timestamp"].dt.timetz
)
```

## Normalize timestamps

```python
normalized = (
    df["timestamp"]
    .dt.normalize()
)
```

Useful when comparing calendar dates while retaining a Pandas datetime dtype.

### Recommendation

For analysis:

```python
datetime64[...] at midnight
```

is usually preferable to Python `date` objects because it retains vectorized datetime operations.

### Documentation

- Datetime accessor: https://pandas.pydata.org/docs/reference/api/pandas.Series.dt.html
- `normalize`: https://pandas.pydata.org/docs/reference/api/pandas.Series.dt.normalize.html

---

# Time Zones

Timezone handling is one of the most important datetime topics.

## Recommended architecture

A common robust strategy:

1. ingest timestamps
2. normalize them to UTC
3. store/process in UTC
4. convert to local timezone only for display or local-calendar logic

Example:

```python
df["timestamp"] = pd.to_datetime(
    df["timestamp"],
    utc=True
)
```

## Localize a naive datetime

If a timestamp represents local clock time but has no timezone:

```python
df["timestamp"] = (
    df["timestamp"]
    .dt.tz_localize(
        "America/Chicago"
    )
)
```

`tz_localize()` assigns a timezone without changing the displayed clock time.

Example conceptually:

```text
2026-09-15 09:00
```

becomes:

```text
2026-09-15 09:00-05:00
```

during CDT.

## Convert an aware datetime

```python
df["timestamp"] = (
    df["timestamp"]
    .dt.tz_convert(
        "UTC"
    )
)
```

`tz_convert()` changes the displayed wall-clock time while preserving the same instant.

## Localize vs convert

### `tz_localize()`

Use when:

```text
timestamp has no timezone
```

and you know what timezone it represents.

### `tz_convert()`

Use when:

```text
timestamp already has timezone information
```

and you want a different timezone.

## Example

```python
s = pd.Series(
    pd.to_datetime([
        "2026-09-15 09:00:00"
    ])
)

central = (
    s.dt.tz_localize(
        "America/Chicago"
    )
)

utc = (
    central.dt.tz_convert(
        "UTC"
    )
)
```

The local clock time changes, but the instant is the same.

## Remove timezone while preserving local clock time

```python
naive_local = (
    aware_series
    .dt.tz_localize(None)
)
```

## Convert to UTC then remove timezone

```python
naive_utc = (
    aware_series
    .dt.tz_convert("UTC")
    .dt.tz_localize(None)
)
```

These are not equivalent.

## Compare timezone-aware timestamps

Both sides must represent compatible timezone-aware values.

```python
cutoff = pd.Timestamp(
    "2026-09-15",
    tz="UTC"
)

df[
    df["timestamp"] >= cutoff
]
```

## Current UTC time

```python
now_utc = pd.Timestamp.now(
    tz="UTC"
)
```

## Current local time

```python
now_central = pd.Timestamp.now(
    tz="America/Chicago"
)
```

### Documentation

- Time zone handling: https://pandas.pydata.org/docs/user_guide/timeseries.html#time-zone-handling
- `tz_localize`: https://pandas.pydata.org/docs/reference/api/pandas.Series.dt.tz_localize.html
- `tz_convert`: https://pandas.pydata.org/docs/reference/api/pandas.Series.dt.tz_convert.html

---

# Daylight Saving Time

Daylight Saving Time creates two major problems when localizing naive timestamps:

- **nonexistent times** during the spring-forward transition
- **ambiguous times** during the fall-back transition

## Nonexistent local time

A local time may never occur.

Example conceptually:

```text
02:30
```

may be skipped when clocks jump from 02:00 to 03:00.

Handle:

```python
localized = (
    series.dt.tz_localize(
        "America/Chicago",
        nonexistent="shift_forward"
    )
)
```

Other strategies include:

```python
nonexistent="NaT"
nonexistent="raise"
```

## Ambiguous local time

During fall-back, the same clock time may occur twice.

Handle:

```python
localized = (
    series.dt.tz_localize(
        "America/Chicago",
        ambiguous="infer"
    )
)
```

Other strategies include:

```python
ambiguous="NaT"
ambiguous="raise"
```

Or supply a boolean array specifying which occurrence belongs to DST.

## Best practice

If the source system can produce UTC timestamps, ingest UTC instead of reconstructing instants from ambiguous local clock times.

### Documentation

- DST and timezone localization: https://pandas.pydata.org/docs/user_guide/timeseries.html#ambiguous-times-when-localizing

---

# Unix Epoch Timestamps

Unix timestamps represent elapsed time from:

```text
1970-01-01 00:00:00 UTC
```

## Seconds

```python
df["timestamp"] = pd.to_datetime(
    df["epoch"],
    unit="s",
    utc=True
)
```

## Milliseconds

```python
df["timestamp"] = pd.to_datetime(
    df["epoch_ms"],
    unit="ms",
    utc=True
)
```

## Microseconds

```python
df["timestamp"] = pd.to_datetime(
    df["epoch_us"],
    unit="us",
    utc=True
)
```

## Nanoseconds

```python
df["timestamp"] = pd.to_datetime(
    df["epoch_ns"],
    unit="ns",
    utc=True
)
```

## Common mistake

These may look similar:

```text
1700000000
1700000000000
```

but the first may be seconds while the second may be milliseconds.

Using the wrong unit produces dates that are wildly incorrect or out of range.

## Convert datetime to Unix seconds

For timezone-aware UTC timestamps:

```python
epoch_seconds = (
    df["timestamp"]
    .astype("int64")
    // 1_000_000_000
)
```

Be cautious with missing `NaT`, because internal integer representations use a sentinel value.

### Documentation

- `to_datetime`: https://pandas.pydata.org/docs/reference/api/pandas.to_datetime.html

---

# Timedeltas and Durations

A `Timedelta` represents elapsed time, not a calendar timestamp.

## Single Timedelta

```python
delta = pd.Timedelta(
    "2 days 3 hours"
)
```

## Construct directly

```python
delta = pd.Timedelta(
    days=2,
    hours=3,
    minutes=15
)
```

## Parse duration column

```python
df["duration"] = pd.to_timedelta(
    df["duration_text"]
)
```

## Numeric durations

```python
df["duration"] = pd.to_timedelta(
    df["duration_seconds"],
    unit="s"
)
```

## Subtract timestamps

```python
df["duration"] = (
    df["end_time"]
    - df["start_time"]
)
```

Result dtype:

```text
timedelta64[ns]
```

## Extract total seconds

```python
df["seconds"] = (
    df["duration"]
    .dt.total_seconds()
)
```

## Duration components

```python
components = (
    df["duration"]
    .dt.components
)
```

Fields include:

```text
days
hours
minutes
seconds
milliseconds
microseconds
nanoseconds
```

## Add duration to datetime

```python
df["expires_at"] = (
    df["created_at"]
    + pd.Timedelta(
        days=7
    )
)
```

## Compare durations

```python
df[
    df["duration"]
    > pd.Timedelta(
        hours=1
    )
]
```

## Timedelta range

```python
pd.timedelta_range(
    start="0 days",
    periods=5,
    freq="2h"
)
```

### Timedelta vs DateOffset

`Timedelta(days=30)` means exactly 30 elapsed days.

A calendar offset such as one month means a calendar-month shift, whose elapsed duration depends on the month.

### Documentation

- Timedelta: https://pandas.pydata.org/docs/reference/api/pandas.Timedelta.html
- `to_timedelta`: https://pandas.pydata.org/docs/reference/api/pandas.to_timedelta.html
- Timedelta operations: https://pandas.pydata.org/docs/user_guide/timedeltas.html

---

# Date Ranges and Frequencies

## Daily date range

```python
dates = pd.date_range(
    start="2026-01-01",
    end="2026-01-07",
    freq="D"
)
```

## Fixed number of periods

```python
dates = pd.date_range(
    start="2026-01-01",
    periods=10,
    freq="D"
)
```

## Hourly range

```python
dates = pd.date_range(
    start="2026-01-01",
    periods=24,
    freq="h"
)
```

## 15-minute range

```python
dates = pd.date_range(
    start="2026-01-01",
    periods=20,
    freq="15min"
)
```

## Month start

```python
dates = pd.date_range(
    start="2026-01-01",
    periods=12,
    freq="MS"
)
```

## Month end

```python
dates = pd.date_range(
    start="2026-01-01",
    periods=12,
    freq="ME"
)
```

## Quarter start

```python
dates = pd.date_range(
    start="2026-01-01",
    periods=4,
    freq="QS"
)
```

## Year start

```python
dates = pd.date_range(
    start="2026-01-01",
    periods=5,
    freq="YS"
)
```

## Business days

```python
dates = pd.bdate_range(
    start="2026-01-01",
    periods=10
)
```

## Timezone-aware range

```python
dates = pd.date_range(
    start="2026-01-01",
    periods=5,
    freq="D",
    tz="America/Chicago"
)
```

## Common frequency aliases

| Frequency | Typical alias |
|---|---|
| nanosecond | `ns` |
| microsecond | `us` |
| millisecond | `ms` |
| second | `s` |
| minute | `min` |
| hour | `h` |
| day | `D` |
| business day | `B` |
| week | `W` |
| month start | `MS` |
| month end | `ME` |
| quarter start | `QS` |
| quarter end | `QE` |
| year start | `YS` |
| year end | `YE` |

Prefer currently documented aliases because older aliases may be deprecated over time.

### Documentation

- Date offsets / frequency aliases: https://pandas.pydata.org/docs/user_guide/timeseries.html#dateoffset-objects
- `date_range`: https://pandas.pydata.org/docs/reference/api/pandas.date_range.html
- `bdate_range`: https://pandas.pydata.org/docs/reference/api/pandas.bdate_range.html

---

# Offsets and Business Dates

Date offsets represent calendar-aware movement.

```python
from pandas.tseries import offsets
```

## Add one day

```python
ts + pd.DateOffset(
    days=1
)
```

## Add one month

```python
ts + pd.DateOffset(
    months=1
)
```

This differs from:

```python
ts + pd.Timedelta(
    days=30
)
```

because calendar months vary in length.

## Month end

```python
ts + offsets.MonthEnd(0)
```

Next month end:

```python
ts + offsets.MonthEnd(1)
```

## Business day

```python
ts + offsets.BDay(1)
```

## Business month end

```python
ts + offsets.BusinessMonthEnd(0)
```

## Custom business day

```python
custom_bday = offsets.CustomBusinessDay(
    weekmask="Mon Tue Wed Thu Fri",
    holidays=[
        "2026-01-01",
        "2026-12-25"
    ]
)

next_day = ts + custom_bday
```

## Week offset

```python
ts + offsets.Week(
    weekday=0
)
```

`weekday=0` represents Monday.

### Documentation

- Date offsets: https://pandas.pydata.org/docs/reference/offset_frequency.html
- `DateOffset`: https://pandas.pydata.org/docs/reference/api/pandas.DateOffset.html

---

# Periods and PeriodIndex

A Timestamp represents a point in time.

A Period represents a calendar interval such as:

- month
- quarter
- year

## Monthly period

```python
p = pd.Period(
    "2026-09",
    freq="M"
)
```

## Quarterly period

```python
p = pd.Period(
    "2026Q3",
    freq="Q"
)
```

## Convert timestamp to period

```python
df["month"] = (
    df["timestamp"]
    .dt.to_period("M")
)
```

## Convert to quarter

```python
df["quarter"] = (
    df["timestamp"]
    .dt.to_period("Q")
)
```

## Period range

```python
periods = pd.period_range(
    start="2026-01",
    periods=12,
    freq="M"
)
```

## Convert period back to timestamp

```python
periods.to_timestamp()
```

End of each period:

```python
periods.to_timestamp(
    how="end"
)
```

## Period arithmetic

```python
p + 1
```

For monthly periods, that means the next calendar month.

## Why Period can be useful

For reporting/grouping, this:

```text
2026-09
```

is often semantically better represented as a month period than as:

```text
2026-09-01 00:00:00
```

### Documentation

- Period: https://pandas.pydata.org/docs/reference/api/pandas.Period.html
- PeriodIndex: https://pandas.pydata.org/docs/reference/api/pandas.PeriodIndex.html
- Period ranges: https://pandas.pydata.org/docs/reference/api/pandas.period_range.html

---

# DatetimeIndex

A `DatetimeIndex` enables powerful time-series selection and resampling.

## Set datetime index

```python
df["timestamp"] = pd.to_datetime(
    df["timestamp"]
)

df = df.set_index(
    "timestamp"
)
```

## Ensure sorted order

```python
df = df.sort_index()
```

## Inspect timezone

```python
df.index.tz
```

## Select one date

```python
df.loc[
    "2026-09-15"
]
```

## Select one month

```python
df.loc[
    "2026-09"
]
```

## Slice date range

```python
df.loc[
    "2026-09-01":
    "2026-09-30"
]
```

## Date components from index

```python
df.index.year
df.index.month
df.index.day
df.index.hour
```

## Normalize index

```python
df.index = (
    df.index.normalize()
)
```

## Floor index

```python
df.index = (
    df.index.floor("h")
)
```

### Documentation

- DatetimeIndex: https://pandas.pydata.org/docs/reference/api/pandas.DatetimeIndex.html

---

# Filtering by Date and Time

Assume:

```python
df["timestamp"] = pd.to_datetime(
    df["timestamp"],
    utc=True
)
```

## After a date

```python
df[
    df["timestamp"]
    >= pd.Timestamp(
        "2026-01-01",
        tz="UTC"
    )
]
```

## Before a date

```python
df[
    df["timestamp"]
    < pd.Timestamp(
        "2027-01-01",
        tz="UTC"
    )
]
```

## Date interval

A half-open interval is often safest:

```python
start = pd.Timestamp(
    "2026-09-01",
    tz="UTC"
)

end = pd.Timestamp(
    "2026-10-01",
    tz="UTC"
)

result = df[
    (df["timestamp"] >= start)
    & (df["timestamp"] < end)
]
```

This avoids needing to invent an end-of-day maximum timestamp.

## One calendar day

```python
day = pd.Timestamp(
    "2026-09-15",
    tz="UTC"
)

next_day = (
    day
    + pd.Timedelta(days=1)
)

result = df[
    (df["timestamp"] >= day)
    & (df["timestamp"] < next_day)
]
```

## Filter by year

```python
df[
    df["timestamp"].dt.year
    == 2026
]
```

## Filter by month

```python
df[
    df["timestamp"].dt.month
    == 9
]
```

## Weekdays only

```python
df[
    df["timestamp"].dt.dayofweek
    < 5
]
```

## Business hours

```python
df[
    df["timestamp"].dt.hour
    .between(9, 16)
]
```

## `between_time` with DatetimeIndex

```python
df = df.set_index(
    "timestamp"
)

business_hours = (
    df.between_time(
        "09:00",
        "17:00"
    )
)
```

## `at_time`

```python
rows_at_noon = (
    df.at_time(
        "12:00"
    )
)
```

### Documentation

- Time-series indexing: https://pandas.pydata.org/docs/user_guide/timeseries.html#indexing

---

# Resampling Time Series

Resampling changes time-series frequency.

Requires a DatetimeIndex, PeriodIndex, TimedeltaIndex, or an `on=` datetime column.

## Set datetime index

```python
df = (
    df
    .set_index("timestamp")
    .sort_index()
)
```

## Daily sum

```python
daily = (
    df["sales"]
    .resample("D")
    .sum()
)
```

## Weekly mean

```python
weekly = (
    df["sales"]
    .resample("W")
    .mean()
)
```

## Monthly aggregation

```python
monthly = (
    df["sales"]
    .resample("ME")
    .sum()
)
```

## Multiple aggregations

```python
daily = (
    df
    .resample("D")
    .agg({
        "sales": "sum",
        "temperature": "mean"
    })
)
```

## Resample using a datetime column

```python
daily = (
    df
    .resample(
        "D",
        on="timestamp"
    )
    .sum(
        numeric_only=True
    )
)
```

## Downsampling

Example:

```text
minute data -> daily data
```

Typically uses aggregation.

## Upsampling

Example:

```text
daily data -> hourly data
```

```python
hourly = (
    daily
    .resample("h")
    .asfreq()
)
```

Then optionally fill:

```python
hourly = (
    hourly.ffill()
)
```

or interpolate:

```python
hourly = (
    hourly.interpolate()
)
```

## Label and closed interval control

```python
result = (
    df["value"]
    .resample(
        "D",
        label="left",
        closed="left"
    )
    .sum()
)
```

These settings matter at time-bin boundaries.

### Documentation

- Resampling: https://pandas.pydata.org/docs/user_guide/timeseries.html#resampling
- `resample`: https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.resample.html

---

# Rolling, Expanding, and EWM Windows

## Rolling row window

```python
df["rolling_mean"] = (
    df["value"]
    .rolling(
        window=7
    )
    .mean()
)
```

## Time-based rolling window

With DatetimeIndex:

```python
df["rolling_24h"] = (
    df["value"]
    .rolling("24h")
    .mean()
)
```

This is different from:

```python
rolling(24)
```

which means 24 rows.

## Minimum required observations

```python
df["rolling_mean"] = (
    df["value"]
    .rolling(
        window=7,
        min_periods=1
    )
    .mean()
)
```

## Rolling sum

```python
df["rolling_sum"] = (
    df["value"]
    .rolling(7)
    .sum()
)
```

## Expanding window

Uses all observations from the beginning through the current row.

```python
df["running_mean"] = (
    df["value"]
    .expanding()
    .mean()
)
```

## Exponentially weighted mean

```python
df["ewm"] = (
    df["value"]
    .ewm(
        span=10,
        adjust=False
    )
    .mean()
)
```

### Documentation

- Window operations: https://pandas.pydata.org/docs/user_guide/window.html
- `rolling`: https://pandas.pydata.org/docs/reference/api/pandas.Series.rolling.html
- `expanding`: https://pandas.pydata.org/docs/reference/api/pandas.Series.expanding.html
- `ewm`: https://pandas.pydata.org/docs/reference/api/pandas.Series.ewm.html

---

# Datetime Missing Values and NaT

`pd.NaT` means "Not a Time."

It is the datetime/timedelta equivalent of missing data.

## Create NaT

```python
value = pd.NaT
```

## Invalid parse to NaT

```python
pd.to_datetime(
    "invalid",
    errors="coerce"
)
# NaT
```

## Detect NaT

Use normal missing-value methods:

```python
df["timestamp"].isna()
```

or:

```python
pd.isna(
    df["timestamp"]
)
```

## Count missing datetimes

```python
df["timestamp"].isna().sum()
```

## Drop missing datetimes

```python
df = df.dropna(
    subset=["timestamp"]
)
```

## Do not compare directly

Avoid:

```python
df["timestamp"] == pd.NaT
```

Use:

```python
df["timestamp"].isna()
```

### Documentation

- `NaT`: https://pandas.pydata.org/docs/reference/api/pandas.NaT.html

---

# Datetime I/O

Datetime behavior can change depending on storage format.

## CSV

CSV has no native datetime type.

When saving:

```python
df.to_csv(
    "output.csv",
    index=False
)
```

timestamps become text.

When reading:

```python
df = pd.read_csv(
    "output.csv",
    parse_dates=["timestamp"]
)
```

Or parse explicitly afterward:

```python
df["timestamp"] = pd.to_datetime(
    df["timestamp"],
    utc=True
)
```

## Parquet

Parquet preserves typed columns much better than CSV.

```python
df.to_parquet(
    "output.parquet",
    index=False
)
```

```python
df = pd.read_parquet(
    "output.parquet"
)
```

Datetime and timezone metadata are generally much better preserved.

## JSON

ISO-oriented export:

```python
df.to_json(
    "output.json",
    orient="records",
    date_format="iso"
)
```

## SQL databases

Datetime handling depends on:

- database engine
- SQL column type
- driver
- timezone policy

Always verify round-trip behavior.

### Documentation

- I/O: https://pandas.pydata.org/docs/user_guide/io.html

---

# Datetime Pitfalls

## 1. String dates are not datetimes

This:

```python
df["date"] = "2026-09-15"
```

is text unless parsed.

Check:

```python
df["date"].dtype
```

Convert:

```python
df["date"] = pd.to_datetime(
    df["date"]
)
```

---

## 2. Naive and timezone-aware timestamps are different

This may fail:

```python
naive_series >= aware_timestamp
```

Normalize both sides to a consistent timezone strategy.

---

## 3. `tz_localize()` and `tz_convert()` are not interchangeable

Use:

```python
tz_localize()
```

to assign timezone meaning to naive timestamps.

Use:

```python
tz_convert()
```

to convert already-aware timestamps to another timezone.

---

## 4. Date-only conversion may create `object` dtype

```python
df["date"] = (
    df["timestamp"].dt.date
)
```

For vectorized operations, often prefer:

```python
df["date"] = (
    df["timestamp"]
    .dt.normalize()
)
```

---

## 5. Month duration is not always 30 days

This:

```python
pd.Timedelta(days=30)
```

is exactly 30 elapsed days.

This:

```python
pd.DateOffset(months=1)
```

means one calendar month.

---

## 6. DST makes local time complicated

Local timestamps may be:

- ambiguous
- nonexistent

UTC storage avoids many of these problems.

---

## 7. Epoch units matter

These are different:

```python
unit="s"
unit="ms"
unit="us"
unit="ns"
```

Always verify the source unit.

---

## 8. Prefer half-open intervals

Instead of:

```python
timestamp <= "2026-09-30 23:59:59.999999999"
```

prefer:

```python
timestamp < "2026-10-01"
```

This is clearer and safer.

---

## 9. Formatting creates strings

```python
df["timestamp"].dt.strftime(...)
```

is for presentation, not continued datetime computation.

---

## 10. Sort before many time-series operations

```python
df = df.sort_values(
    "timestamp"
)
```

or with DatetimeIndex:

```python
df = df.sort_index()
```

Chronological order matters for:

- rolling calculations
- differences
- shifts
- as-of merges
- forward/backward fills

---

# Grouping and Aggregation

## Basic groupby

```python
df.groupby(
    "city"
)["age"].mean()
```

## Multiple grouping columns

```python
df.groupby(
    ["city", "gender"]
)["age"].mean()
```

## Multiple aggregations

```python
summary = (
    df
    .groupby("city")
    .agg({
        "age": [
            "mean",
            "min",
            "max"
        ],
        "salary": "sum"
    })
)
```

## Named aggregation

Produces clean output column names:

```python
summary = (
    df
    .groupby(
        "city",
        as_index=False
    )
    .agg(
        avg_age=(
            "age",
            "mean"
        ),
        max_age=(
            "age",
            "max"
        ),
        total_salary=(
            "salary",
            "sum"
        )
    )
)
```

## Count rows per group

```python
df.groupby(
    "city"
).size()
```

## Count non-null values

```python
df.groupby(
    "city"
)["age"].count()
```

`size()` counts rows, while `count()` excludes missing values.

## Preserve grouping column as column

```python
summary = (
    df
    .groupby(
        "city",
        as_index=False
    )["age"]
    .mean()
)
```

## Group by time period

```python
df["month"] = (
    df["timestamp"]
    .dt.to_period("M")
)

monthly = (
    df
    .groupby("month")
    ["sales"]
    .sum()
)
```

## Group by `Grouper`

```python
monthly = (
    df
    .groupby(
        pd.Grouper(
            key="timestamp",
            freq="ME"
        )
    )
    ["sales"]
    .sum()
)
```

### Documentation

- GroupBy: https://pandas.pydata.org/docs/user_guide/groupby.html
- `groupby`: https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.groupby.html

---

# Transform and Filter by Group

## `transform`

Returns output aligned to original rows.

Example: group mean on every row.

```python
df["city_avg_age"] = (
    df
    .groupby("city")
    ["age"]
    .transform("mean")
)
```

## Difference from aggregate

`agg()` usually reduces rows.

`transform()` usually preserves row count.

## Center values within group

```python
df["age_centered"] = (
    df["age"]
    - df
      .groupby("city")
      ["age"]
      .transform("mean")
)
```

## Filter whole groups

Keep cities with at least 100 rows:

```python
filtered = (
    df
    .groupby("city")
    .filter(
        lambda group:
        len(group) >= 100
    )
)
```

## Rank within group

```python
df["rank"] = (
    df
    .groupby("city")
    ["sales"]
    .rank(
        ascending=False
    )
)
```

### Documentation

- GroupBy transform/filter: https://pandas.pydata.org/docs/user_guide/groupby.html

---

# Merging and Joining

## Inner join

```python
merged = pd.merge(
    df1,
    df2,
    on="id",
    how="inner"
)
```

## Left join

```python
merged = pd.merge(
    df1,
    df2,
    on="id",
    how="left"
)
```

## Right join

```python
merged = pd.merge(
    df1,
    df2,
    on="id",
    how="right"
)
```

## Outer join

```python
merged = pd.merge(
    df1,
    df2,
    on="id",
    how="outer"
)
```

## Different key names

```python
merged = pd.merge(
    df1,
    df2,
    left_on="customer_id",
    right_on="id",
    how="left"
)
```

## Multiple keys

```python
merged = pd.merge(
    df1,
    df2,
    on=[
        "customer_id",
        "date"
    ],
    how="left"
)
```

## Merge with index

```python
merged = pd.merge(
    df1,
    df2,
    left_index=True,
    right_index=True,
    how="left"
)
```

## Validate merge cardinality

This is extremely useful.

One-to-one:

```python
merged = pd.merge(
    df1,
    df2,
    on="id",
    validate="one_to_one"
)
```

Many-to-one:

```python
merged = pd.merge(
    transactions,
    customers,
    on="customer_id",
    validate="many_to_one"
)
```

Other validation values include:

```text
one_to_one
one_to_many
many_to_one
many_to_many
```

## Indicator column

```python
merged = pd.merge(
    df1,
    df2,
    on="id",
    how="outer",
    indicator=True
)
```

Adds:

```text
_merge
```

with values:

```text
left_only
right_only
both
```

Useful for reconciliation.

## Suffixes

```python
merged = pd.merge(
    df1,
    df2,
    on="id",
    suffixes=(
        "_left",
        "_right"
    )
)
```

## Join nearest timestamp with `merge_asof`

For time-aligned data:

```python
left = left.sort_values(
    "timestamp"
)

right = right.sort_values(
    "timestamp"
)

merged = pd.merge_asof(
    left,
    right,
    on="timestamp",
    direction="backward"
)
```

Optional tolerance:

```python
merged = pd.merge_asof(
    left,
    right,
    on="timestamp",
    tolerance=pd.Timedelta(
        minutes=5
    ),
    direction="nearest"
)
```

### Documentation

- Merge/join: https://pandas.pydata.org/docs/user_guide/merging.html
- `merge`: https://pandas.pydata.org/docs/reference/api/pandas.merge.html
- `merge_asof`: https://pandas.pydata.org/docs/reference/api/pandas.merge_asof.html

---

# Concatenation

## Append rows

Use `pd.concat()`, not the removed/deprecated historical `DataFrame.append()` pattern.

```python
combined = pd.concat(
    [df1, df2],
    axis=0
)
```

Reset row index:

```python
combined = pd.concat(
    [df1, df2],
    ignore_index=True
)
```

## Concatenate columns

```python
combined = pd.concat(
    [df1, df2],
    axis=1
)
```

## Add source keys

```python
combined = pd.concat(
    {
        "train": train_df,
        "test": test_df
    }
)
```

Creates a hierarchical index identifying source.

## Verify no duplicate index

```python
combined = pd.concat(
    [df1, df2],
    verify_integrity=True
)
```

### Documentation

- `concat`: https://pandas.pydata.org/docs/reference/api/pandas.concat.html

---

# Reshaping

## Wide to long with `melt`

Wide:

```text
id  jan  feb  mar
1   10   20   30
2   40   50   60
```

```python
long = df.melt(
    id_vars="id",
    var_name="month",
    value_name="sales"
)
```

Long:

```text
id  month  sales
1   jan    10
1   feb    20
...
```

## Long to wide with `pivot`

```python
wide = df.pivot(
    index="id",
    columns="month",
    values="sales"
)
```

`pivot()` requires unique combinations.

## `pivot_table`

Handles duplicates by aggregating.

```python
wide = pd.pivot_table(
    df,
    index="id",
    columns="month",
    values="sales",
    aggfunc="sum"
)
```

## Stack

Columns → index level.

```python
stacked = df.stack()
```

## Unstack

Index level → columns.

```python
wide = stacked.unstack()
```

## Crosstab

```python
table = pd.crosstab(
    df["region"],
    df["status"]
)
```

Normalized:

```python
table = pd.crosstab(
    df["region"],
    df["status"],
    normalize="index"
)
```

## Explode list-like values

```python
df = pd.DataFrame({
    "id": [1, 2],
    "tags": [
        ["python", "pandas"],
        ["sql"]
    ]
})

exploded = df.explode(
    "tags"
)
```

### Documentation

- Reshaping: https://pandas.pydata.org/docs/user_guide/reshaping.html
- `melt`: https://pandas.pydata.org/docs/reference/api/pandas.melt.html
- `pivot`: https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.pivot.html
- `explode`: https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.explode.html

---

# Pivot Tables

## Basic pivot table

```python
pivot = pd.pivot_table(
    df,
    values="sales",
    index="region",
    columns="product",
    aggfunc="sum"
)
```

## Multiple aggregations

```python
pivot = pd.pivot_table(
    df,
    values="sales",
    index="region",
    aggfunc=[
        "sum",
        "mean"
    ]
)
```

## Fill missing cells

```python
pivot = pd.pivot_table(
    df,
    values="sales",
    index="region",
    columns="product",
    aggfunc="sum",
    fill_value=0
)
```

## Margins / totals

```python
pivot = pd.pivot_table(
    df,
    values="sales",
    index="region",
    columns="product",
    aggfunc="sum",
    margins=True
)
```

### Documentation

- `pivot_table`: https://pandas.pydata.org/docs/reference/api/pandas.pivot_table.html

---

# Apply, Map, and Vectorized Alternatives

`apply()` is flexible but often slower than vectorized Pandas/NumPy operations.

## Series `map`

```python
mapping = {
    "M": "Male",
    "F": "Female"
}

df["gender_label"] = (
    df["gender"].map(
        mapping
    )
)
```

## Series `apply`

```python
df["age_group"] = (
    df["age"]
    .apply(
        lambda x:
        "adult"
        if x >= 18
        else "minor"
    )
)
```

## DataFrame row-wise apply

```python
df["full_name"] = (
    df.apply(
        lambda row:
        row["first_name"]
        + " "
        + row["last_name"],
        axis=1
    )
)
```

Prefer vectorized string operations:

```python
df["full_name"] = (
    df["first_name"]
    + " "
    + df["last_name"]
)
```

## Conditional alternatives

Instead of `apply`:

```python
df["is_adult"] = (
    df["age"] >= 18
)
```

or:

```python
df["label"] = np.where(
    df["age"] >= 18,
    "adult",
    "minor"
)
```

## Mapping with fallback

```python
df["label"] = (
    df["code"]
    .map(mapping)
    .fillna("Unknown")
)
```

### Rule of thumb

Prefer, in roughly this order:

1. direct vectorized arithmetic/comparison
2. `.str`, `.dt`, `.cat` accessors
3. built-in Pandas methods
4. `map`
5. `apply`
6. Python row loops

### Documentation

- Function application: https://pandas.pydata.org/docs/user_guide/basics.html#function-application

---

# Window Functions

## Shift

Previous row:

```python
df["previous_sales"] = (
    df["sales"].shift(1)
)
```

Next row:

```python
df["next_sales"] = (
    df["sales"].shift(-1)
)
```

## Difference

```python
df["sales_change"] = (
    df["sales"].diff()
)
```

Equivalent conceptually to:

```text
current - previous
```

## Percent change

```python
df["pct_change"] = (
    df["sales"]
    .pct_change()
)
```

## Cumulative sum

```python
df["running_total"] = (
    df["sales"]
    .cumsum()
)
```

## Cumulative max

```python
df["running_max"] = (
    df["sales"]
    .cummax()
)
```

## Group-aware shift

```python
df["previous_sales"] = (
    df
    .groupby("customer_id")
    ["sales"]
    .shift(1)
)
```

## Rank

```python
df["rank"] = (
    df["sales"]
    .rank(
        ascending=False,
        method="dense"
    )
)
```

### Documentation

- Windowing operations: https://pandas.pydata.org/docs/user_guide/window.html

---

# Exporting Data

## CSV

```python
df.to_csv(
    "output.csv",
    index=False
)
```

## Excel

```python
df.to_excel(
    "output.xlsx",
    index=False
)
```

## JSON

```python
df.to_json(
    "output.json",
    orient="records"
)
```

Readable JSON:

```python
df.to_json(
    "output.json",
    orient="records",
    indent=2
)
```

## Parquet

```python
df.to_parquet(
    "output.parquet",
    index=False
)
```

## Pickle

```python
df.to_pickle(
    "output.pkl"
)
```

Only load pickle files from trusted sources.

## SQL

```python
df.to_sql(
    "table_name",
    connection,
    if_exists="append",
    index=False
)
```

## Clipboard

```python
df.to_clipboard(
    index=False
)
```

### Documentation

- I/O: https://pandas.pydata.org/docs/user_guide/io.html

---

# Common Data Cleaning Patterns

## Clean column names

```python
df.columns = (
    df.columns
    .str.strip()
    .str.lower()
    .str.replace(
        r"\s+",
        "_",
        regex=True
    )
    .str.replace(
        r"[^a-z0-9_]",
        "",
        regex=True
    )
)
```

## Convert numeric text

```python
df["amount"] = pd.to_numeric(
    df["amount"],
    errors="coerce"
)
```

## Currency text

```python
df["amount"] = (
    df["amount"]
    .str.replace(
        "$",
        "",
        regex=False
    )
    .str.replace(
        ",",
        "",
        regex=False
    )
)

df["amount"] = pd.to_numeric(
    df["amount"],
    errors="coerce"
)
```

## Percent text

```python
df["percent"] = (
    df["percent"]
    .str.rstrip("%")
    .astype(float)
    / 100
)
```

## Normalize whitespace

```python
df["name"] = (
    df["name"]
    .str.replace(
        r"\s+",
        " ",
        regex=True
    )
    .str.strip()
)
```

## Normalize capitalization

```python
df["city"] = (
    df["city"]
    .str.strip()
    .str.title()
)
```

## Replace empty strings with missing

```python
df = df.replace(
    r"^\s*$",
    pd.NA,
    regex=True
)
```

## Clean IDs without losing leading zeros

Treat identifiers as strings:

```python
df["zip_code"] = (
    df["zip_code"]
    .astype("string")
    .str.zfill(5)
)
```

Do not model ZIP codes, account numbers, product IDs, etc. as numeric measurements merely because they contain digits.

## Parse datetime safely

```python
df["timestamp"] = pd.to_datetime(
    df["timestamp"],
    errors="coerce",
    utc=True
)
```

Then inspect failures:

```python
bad_rows = df[
    df["timestamp"].isna()
]
```

### Documentation

- Working with text: https://pandas.pydata.org/docs/user_guide/text.html
- Missing data: https://pandas.pydata.org/docs/user_guide/missing_data.html

---

# Common Analysis Patterns

## Frequency table

```python
counts = (
    df["category"]
    .value_counts()
)
```

## Percent distribution

```python
percent = (
    df["category"]
    .value_counts(
        normalize=True
    )
    .mul(100)
)
```

## Top N by metric

```python
top = df.nlargest(
    10,
    "sales"
)
```

## Grouped summary

```python
summary = (
    df
    .groupby(
        "region",
        as_index=False
    )
    .agg(
        rows=("id", "size"),
        avg_sales=("sales", "mean"),
        total_sales=("sales", "sum")
    )
)
```

## Cumulative percentage

```python
summary = (
    df
    .groupby("category")
    ["sales"]
    .sum()
    .sort_values(
        ascending=False
    )
    .to_frame()
)

summary["share"] = (
    summary["sales"]
    / summary["sales"].sum()
)

summary["cumulative_share"] = (
    summary["share"]
    .cumsum()
)
```

## Random sample

```python
sample = df.sample(
    n=100,
    random_state=42
)
```

## Fractional sample

```python
sample = df.sample(
    frac=0.1,
    random_state=42
)
```

## Manual train/test split

```python
train = df.sample(
    frac=0.8,
    random_state=42
)

test = df.drop(
    train.index
)
```

For ML work, prefer dedicated splitting functions such as scikit-learn's `train_test_split()` when stratification and reproducible ML pipelines matter.

## Lag feature

```python
df = df.sort_values(
    ["user_id", "timestamp"]
)

df["previous_value"] = (
    df
    .groupby("user_id")
    ["value"]
    .shift(1)
)
```

## Time since previous event

```python
df = df.sort_values(
    ["user_id", "timestamp"]
)

df["time_since_previous"] = (
    df
    .groupby("user_id")
    ["timestamp"]
    .diff()
)
```

### Documentation

- GroupBy: https://pandas.pydata.org/docs/user_guide/groupby.html
- Window operations: https://pandas.pydata.org/docs/user_guide/window.html

---

# Performance and Memory

## Prefer vectorized operations

Slow pattern:

```python
for i, row in df.iterrows():
    df.loc[i, "total"] = (
        row["price"]
        * row["quantity"]
    )
```

Prefer:

```python
df["total"] = (
    df["price"]
    * df["quantity"]
)
```

## Avoid repeated row appends

Instead of repeatedly growing a DataFrame inside a loop:

```python
frames = []

for item in items:
    frames.append(
        build_frame(item)
    )

df = pd.concat(
    frames,
    ignore_index=True
)
```

## Prefer appropriate dtypes

Inspect memory:

```python
df.info(
    memory_usage="deep"
)
```

Potential optimizations:

```python
df["small_integer"] = (
    pd.to_numeric(
        df["small_integer"],
        downcast="integer"
    )
)

df["small_float"] = (
    pd.to_numeric(
        df["small_float"],
        downcast="float"
    )
)
```

## Category for repeated labels

```python
df["status"] = (
    df["status"]
    .astype("category")
)
```

Can substantially reduce memory when cardinality is low.

## Read only needed columns

```python
df = pd.read_csv(
    "large.csv",
    usecols=[
        "id",
        "timestamp",
        "value"
    ]
)
```

## Read in chunks

```python
for chunk in pd.read_csv(
    "large.csv",
    chunksize=100_000
):
    ...
```

## Query/eval

Pandas provides:

```python
df.query(...)
df.eval(...)
```

These can improve readability for some expressions, though performance depends on the operation and dataset.

## Avoid unnecessary object dtype

Object columns often:

- consume more memory
- lose specialized vectorized behavior
- hide inconsistent values

Use:

```python
df.convert_dtypes()
```

and explicit type conversion.

### Documentation

- Enhancing performance: https://pandas.pydata.org/docs/user_guide/enhancingperf.html
- Scaling to large datasets: https://pandas.pydata.org/docs/user_guide/scale.html

---

# Copying, Views, and Assignment

Assignment semantics are important in Pandas.

## Avoid chained assignment

Risky:

```python
df[
    df["age"] > 30
]["city"] = "Unknown"
```

Prefer:

```python
df.loc[
    df["age"] > 30,
    "city"
] = "Unknown"
```

## Explicit copy

```python
subset = (
    df.loc[
        df["age"] > 30
    ]
    .copy()
)
```

Use `.copy()` when you intend to create an independent working object.

## Shallow vs deep copy

```python
copy_df = df.copy(
    deep=True
)
```

`deep=True` is the default.

## Copy-on-Write

Modern Pandas includes Copy-on-Write behavior intended to make assignment semantics more predictable and reduce accidental mutation between derived objects.

Code should still prefer explicit, unambiguous assignment patterns using `.loc`.

### Documentation

- Copy-on-Write: https://pandas.pydata.org/docs/user_guide/copy_on_write.html
- Indexing: https://pandas.pydata.org/docs/user_guide/indexing.html

---

# Debugging and Validation

## Check shape

```python
print(
    df.shape
)
```

After joins, filters, and deduplication, always verify expected row counts.

## Check columns

```python
print(
    df.columns.tolist()
)
```

## Check dtypes

```python
print(
    df.dtypes
)
```

## Check missing values

```python
print(
    df.isna().sum()
)
```

## Check uniqueness

```python
df["id"].is_unique
```

## Assert unique ID

```python
assert df["id"].is_unique
```

## Assert expected row count

```python
assert len(df) > 0
```

## Assert no missing values

```python
assert (
    df["id"]
    .notna()
    .all()
)
```

## Assert allowed categories

```python
allowed = {
    "active",
    "inactive"
}

assert (
    df["status"]
    .dropna()
    .isin(allowed)
    .all()
)
```

## Assert date range

```python
assert (
    df["timestamp"]
    >= pd.Timestamp(
        "2020-01-01",
        tz="UTC"
    )
).all()
```

## Compare DataFrames

```python
pd.testing.assert_frame_equal(
    actual,
    expected
)
```

## Compare Series

```python
pd.testing.assert_series_equal(
    actual,
    expected
)
```

## Validate merge cardinality

```python
pd.merge(
    left,
    right,
    on="id",
    validate="many_to_one"
)
```

This catches unintended row multiplication.

## Track rows through pipeline

```python
print(
    "Before:",
    len(df)
)

df = transform(df)

print(
    "After:",
    len(df)
)
```

### Documentation

- Testing utilities: https://pandas.pydata.org/docs/reference/testing.html

---

# Common Gotchas

## 1. Use `&` and `|`, not `and` and `or`

Correct:

```python
df[
    (df["age"] > 25)
    & (df["city"] == "Chicago")
]
```

Incorrect:

```python
# df[
#     df["age"] > 25
#     and df["city"] == "Chicago"
# ]
```

---

## 2. Parenthesize each boolean condition

Correct:

```python
df[
    (df["age"] > 25)
    & (df["salary"] > 50_000)
]
```

---

## 3. Avoid chained assignment

Risky:

```python
df[
    df["age"] > 30
]["city"] = "Unknown"
```

Prefer:

```python
df.loc[
    df["age"] > 30,
    "city"
] = "Unknown"
```

---

## 4. `loc` and `iloc` mean different things

```python
df.loc[5]
```

means index label `5`.

```python
df.iloc[5]
```

means sixth row by position.

---

## 5. `loc` slicing is generally end-inclusive

```python
df.loc[0:5]
```

usually includes label `5`.

```python
df.iloc[0:5]
```

excludes position `5`.

---

## 6. `size()` and `count()` differ

```python
group.size()
```

counts rows.

```python
group["column"].count()
```

counts non-missing values.

---

## 7. Joins can multiply rows

If the join key is duplicated on both sides, a merge can produce a many-to-many expansion.

Use:

```python
validate="one_to_one"
```

or another expected cardinality whenever practical.

---

## 8. `object` dtype can hide mixed types

Inspect:

```python
df["column"].map(type).value_counts()
```

Then convert explicitly.

---

## 9. Integer columns may become floats because of missing values

Traditional:

```text
1
2
NaN
```

may become `float64`.

Use nullable:

```python
dtype="Int64"
```

when appropriate.

---

## 10. String operations need `.str`

Correct:

```python
df["name"].str.lower()
```

---

## 11. Datetime operations need `.dt`

Correct:

```python
df["timestamp"].dt.year
```

---

## 12. Category operations need `.cat`

Correct:

```python
df["status"].cat.categories
```

---

## 13. `apply(axis=1)` can be slow

Prefer vectorized operations when possible.

---

## 14. Avoid relying on `inplace=True`

Instead of:

```python
df.drop(
    columns=["col"],
    inplace=True
)
```

often prefer:

```python
df = df.drop(
    columns=["col"]
)
```

This is clearer in pipelines and does not imply guaranteed memory savings.

---

## 15. CSV does not preserve data types reliably

After reading CSV, explicitly verify:

```python
df.dtypes
```

especially for:

- IDs
- dates
- booleans
- nullable integers
- leading-zero strings

---

## 16. `NaN`, `pd.NA`, `None`, and `NaT` are not identical

Use:

```python
pd.isna(value)
```

instead of writing custom equality checks.

---

## 17. Datetime strings can sort incorrectly

Always parse before chronological operations:

```python
df["timestamp"] = pd.to_datetime(
    df["timestamp"]
)
```

---

## 18. Normalize timezone strategy early

A good default for event timestamps:

```text
store/process in UTC
convert locally only when necessary
```

---

## 19. GroupBy may move grouping keys into the index

Use:

```python
as_index=False
```

or:

```python
.reset_index()
```

when you need normal columns.

---

## 20. Always inspect shape after major transformations

```python
df.shape
```

Especially after:

- merge
- join
- explode
- pivot
- drop_duplicates
- groupby
- filter

---

# Quick Reference

## Import

```python
import pandas as pd
import numpy as np
```

## Create

```python
pd.Series([1, 2, 3])

pd.DataFrame({
    "a": [1, 2],
    "b": [3, 4]
})
```

## Read

```python
pd.read_csv("data.csv")
pd.read_excel("data.xlsx")
pd.read_json("data.json")
pd.read_parquet("data.parquet")
```

## Inspect

```python
df.head()
df.tail()
df.sample(5)

df.shape
df.columns
df.index
df.dtypes
df.info()
df.describe()

df.isna().sum()
df.nunique()
```

## Select

```python
df["column"]
df[["a", "b"]]

df.loc[rows, columns]
df.iloc[rows, columns]

df.at[label, column]
df.iat[row, column]
```

## Filter

```python
df[df["age"] > 30]

df[
    (df["age"] > 25)
    & (df["city"] == "Chicago")
]

df[
    df["city"].isin(
        ["Chicago", "Minneapolis"]
    )
]

df.query(
    "age > 30"
)
```

## Missing

```python
df.isna()
df.notna()

df.dropna()
df.fillna(0)

df.ffill()
df.bfill()

df.interpolate()
```

## Convert types

```python
df.astype({
    "age": "Int64"
})

pd.to_numeric(
    df["amount"],
    errors="coerce"
)

pd.to_datetime(
    df["timestamp"],
    errors="coerce",
    utc=True
)

pd.to_timedelta(
    df["duration"]
)

df.convert_dtypes()
```

## Strings

```python
df["text"].str.lower()
df["text"].str.upper()
df["text"].str.strip()

df["text"].str.contains(
    "value",
    na=False
)

df["text"].str.replace(
    "old",
    "new",
    regex=False
)

df["text"].str.split(
    ",",
    expand=True
)

df["text"].str.extract(
    r"pattern"
)
```

## Datetime parsing

```python
pd.to_datetime(
    values
)

pd.to_datetime(
    values,
    errors="coerce"
)

pd.to_datetime(
    values,
    format="%Y-%m-%d"
)

pd.to_datetime(
    values,
    utc=True
)

pd.to_datetime(
    epochs,
    unit="ms",
    utc=True
)
```

## Datetime components

```python
s.dt.year
s.dt.month
s.dt.day

s.dt.hour
s.dt.minute
s.dt.second

s.dt.dayofweek
s.dt.day_name()

s.dt.dayofyear
s.dt.quarter

s.dt.is_month_start
s.dt.is_month_end
s.dt.is_leap_year

s.dt.isocalendar()
```

## Datetime formatting

```python
s.dt.strftime(
    "%Y-%m-%d"
)

s.dt.strftime(
    "%Y-%m-%d %H:%M:%S"
)
```

## Date-only / time-only

```python
s.dt.date
s.dt.time
s.dt.timetz

s.dt.normalize()
s.dt.floor("D")
```

## Time zones

```python
s.dt.tz_localize(
    "America/Chicago"
)

s.dt.tz_convert(
    "UTC"
)

pd.Timestamp.now(
    tz="UTC"
)
```

## Timedelta

```python
pd.Timedelta(
    days=1
)

pd.to_timedelta(
    values
)

end - start

duration.dt.total_seconds()
duration.dt.components
```

## Date ranges

```python
pd.date_range(
    "2026-01-01",
    periods=10,
    freq="D"
)

pd.bdate_range(
    "2026-01-01",
    periods=10
)

pd.timedelta_range(
    "0 days",
    periods=5,
    freq="h"
)

pd.period_range(
    "2026-01",
    periods=12,
    freq="M"
)
```

## Periods

```python
s.dt.to_period("M")
s.dt.to_period("Q")

periods.to_timestamp()
```

## Time series

```python
df = (
    df
    .set_index("timestamp")
    .sort_index()
)

df.resample("D").sum()

df["value"].rolling(7).mean()

df["value"].rolling("24h").mean()

df["value"].expanding().mean()

df["value"].ewm(
    span=10
).mean()
```

## GroupBy

```python
df.groupby(
    "city"
)["sales"].sum()

df.groupby(
    ["city", "product"]
)["sales"].mean()

df.groupby(
    "city",
    as_index=False
).agg(
    total_sales=(
        "sales",
        "sum"
    ),
    avg_sales=(
        "sales",
        "mean"
    )
)
```

## Merge

```python
pd.merge(
    left,
    right,
    on="id",
    how="left"
)

pd.merge(
    left,
    right,
    on="id",
    validate="many_to_one"
)

pd.merge_asof(
    left,
    right,
    on="timestamp"
)
```

## Reshape

```python
df.melt(
    id_vars="id"
)

df.pivot(
    index="id",
    columns="category",
    values="value"
)

pd.pivot_table(
    df,
    index="region",
    columns="product",
    values="sales",
    aggfunc="sum"
)

df.stack()
df.unstack()
df.explode("tags")
```

## Sort

```python
df.sort_values(
    "sales"
)

df.sort_values(
    ["region", "sales"],
    ascending=[
        True,
        False
    ]
)

df.sort_index()

df.nlargest(
    10,
    "sales"
)
```

## Window / lag

```python
s.shift(1)
s.diff()
s.pct_change()

s.cumsum()
s.cummax()

s.rank()
```

## Export

```python
df.to_csv(
    "output.csv",
    index=False
)

df.to_excel(
    "output.xlsx",
    index=False
)

df.to_json(
    "output.json",
    orient="records"
)

df.to_parquet(
    "output.parquet",
    index=False
)
```

## Validation

```python
assert df["id"].is_unique

assert (
    df["id"]
    .notna()
    .all()
)

pd.testing.assert_frame_equal(
    actual,
    expected
)

pd.testing.assert_series_equal(
    actual,
    expected
)
```

---

# Pandas Function and Method Reference

This reference documents every Pandas function/method used in this cheat sheet. Each entry includes its purpose, a representative example from the guide, important usage notes where they add value, and a direct link to the official Pandas API reference.

## Constructors and Top-Level Functions

### `pd.DataFrame()`

**What it does:** Create a two-dimensional labeled table.

**Example:**

```python
df = pd.DataFrame({
    "name": ["Alice", "Bob", "Charlie"],
    "age": [25, 30, 35],
    "city": ["Minneapolis", "Chicago", "New York"]
})
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.html

---

### `pd.Series()`

**What it does:** Create a one-dimensional labeled array.

**Example:**

```python
s = pd.Series([10, 20, 30])

print(s)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.html

---

### `pd.Timestamp()`

**What it does:** Represent a single datetime-like instant.

**Example:**

```python
ts = pd.Timestamp(
    "2026-09-15 14:30:00"
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Timestamp.html

---

### `pd.Timedelta()`

**What it does:** Represent an elapsed duration.

**Example:**

```python
delta = pd.Timedelta(
    "2 days 3 hours"
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Timedelta.html

---

### `pd.Period()`

**What it does:** Represent a calendar span such as a month, quarter, or year.

**Example:**

```python
p = pd.Period(
    "2026-09",
    freq="M"
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Period.html

---

### `pd.DatetimeIndex()`

**What it does:** Create an index optimized for datetime values.

**Example:**

```python
idx = pd.DatetimeIndex([
    "2026-09-15",
    "2026-09-16"
])
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DatetimeIndex.html

---

### `pd.CategoricalDtype()`

**What it does:** Define categorical labels and optional ordering.

**Example:**

```python
priority_type = pd.CategoricalDtype(
    categories=[
        "low",
        "medium",
        "high"
    ],
    ordered=True
)

df["priority"] = (
    df["priority"]
    .astype(priority_type)
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.CategoricalDtype.html

---

### `pd.DateOffset()`

**What it does:** Represent calendar-aware date arithmetic.

**Example:**

```python
ts + pd.DateOffset(
    days=1
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DateOffset.html

---

### `pd.Grouper()`

**What it does:** Define grouping behavior, especially frequency-based datetime grouping.

**Example:**

```python
monthly = (
    df
    .groupby(
        pd.Grouper(
            key="timestamp",
            freq="ME"
        )
    )
    ["sales"]
    .sum()
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Grouper.html

---

### `pd.read_csv()`

**What it does:** Read CSV or other delimited text into a DataFrame.

**Important notes:** Useful options include `usecols=`, `dtype=`, `parse_dates=`, `na_values=`, `chunksize=`, and `encoding=`.

**Example:**

```python
df = pd.read_csv("data.csv")
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.read_csv.html

---

### `pd.read_excel()`

**What it does:** Read Excel worksheets into DataFrames.

**Example:**

```python
df = pd.read_excel("data.xlsx")
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.read_excel.html

---

### `pd.read_json()`

**What it does:** Read JSON data into Pandas objects.

**Example:**

```python
df = pd.read_json("data.json")
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.read_json.html

---

### `pd.read_parquet()`

**What it does:** Read typed Parquet data into a DataFrame.

**Example:**

```python
df = pd.read_parquet("data.parquet")
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.read_parquet.html

---

### `pd.read_sql_query()`

**What it does:** Execute a SQL query and return the result as a DataFrame.

**Example:**

```python
query = """
SELECT
    user_id,
    created_at,
    amount
FROM transactions
"""

df = pd.read_sql_query(
    query,
    connection
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.read_sql_query.html

---

### `pd.read_html()`

**What it does:** Extract HTML tables into DataFrames.

**Example:**

```python
tables = pd.read_html(
    "https://example.com/page"
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.read_html.html

---

### `pd.read_clipboard()`

**What it does:** Read tabular clipboard content into a DataFrame.

**Example:**

```python
df = pd.read_clipboard()
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.read_clipboard.html

---

### `pd.to_datetime()`

**What it does:** Convert strings, numbers, arrays, or columns into datetime values.

**Important notes:** Key options: `errors='coerce'`, `format=`, `utc=True`, `unit=`, `dayfirst=`, and `yearfirst=`. For production pipelines, make the source format and timezone assumptions explicit.

**Example:**

```python
df["created_at"] = pd.to_datetime(
    df["created_at"],
    errors="coerce"
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.to_datetime.html

---

### `pd.to_timedelta()`

**What it does:** Convert values into elapsed Timedelta values.

**Example:**

```python
df["duration"] = pd.to_timedelta(
    df["duration"],
    errors="coerce"
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.to_timedelta.html

---

### `pd.to_numeric()`

**What it does:** Convert values to numeric dtype with configurable error handling.

**Example:**

```python
df["amount"] = pd.to_numeric(
    df["amount"],
    errors="coerce"
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.to_numeric.html

---

### `pd.date_range()`

**What it does:** Generate a fixed-frequency DatetimeIndex.

**Example:**

```python
dates = pd.date_range(
    start="2026-01-01",
    end="2026-01-07",
    freq="D"
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.date_range.html

---

### `pd.bdate_range()`

**What it does:** Generate a business-day DatetimeIndex.

**Example:**

```python
dates = pd.bdate_range(
    start="2026-01-01",
    periods=10
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.bdate_range.html

---

### `pd.timedelta_range()`

**What it does:** Generate a fixed-frequency TimedeltaIndex.

**Example:**

```python
pd.timedelta_range(
    start="0 days",
    periods=5,
    freq="2h"
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.timedelta_range.html

---

### `pd.period_range()`

**What it does:** Generate a PeriodIndex over calendar periods.

**Example:**

```python
periods = pd.period_range(
    start="2026-01",
    periods=12,
    freq="M"
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.period_range.html

---

### `pd.merge()`

**What it does:** Perform database-style joins between DataFrames.

**Important notes:** Use `validate=` to enforce expected cardinality and catch accidental many-to-many row multiplication. `indicator=True` is useful for reconciliation.

**Example:**

```python
merged = pd.merge(
    df1,
    df2,
    on="id",
    how="inner"
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.merge.html

---

### `pd.merge_asof()`

**What it does:** Join sorted data using nearest or prior/next keys, commonly timestamps.

**Important notes:** Inputs must normally be sorted by the merge key. Use `direction=` and `tolerance=` to constrain matching.

**Example:**

```python
left = left.sort_values(
    "timestamp"
)

right = right.sort_values(
    "timestamp"
)

merged = pd.merge_asof(
    left,
    right,
    on="timestamp",
    direction="backward"
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.merge_asof.html

---

### `pd.concat()`

**What it does:** Combine Pandas objects along rows or columns.

**Important notes:** `axis=0` stacks rows; `axis=1` combines columns by aligned index. `ignore_index=True` creates a new sequential row index.

**Example:**

```python
combined = pd.concat(
    [df1, df2],
    axis=0
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.concat.html

---

### `pd.pivot_table()`

**What it does:** Create an aggregated spreadsheet-style pivot table.

**Important notes:** Core controls are `index=`, `columns=`, `values=`, `aggfunc=`, `fill_value=`, and `margins=`.

**Example:**

```python
wide = pd.pivot_table(
    df,
    index="id",
    columns="month",
    values="sales",
    aggfunc="sum"
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.pivot_table.html

---

### `pd.crosstab()`

**What it does:** Create a frequency table across categorical variables.

**Example:**

```python
table = pd.crosstab(
    df["region"],
    df["status"]
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.crosstab.html

---

### `assert_frame_equal()`

**What it does:** Assert DataFrame equality in tests.

**Example:**

```python
pd.testing.assert_frame_equal(
    actual,
    expected
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.testing.assert_frame_equal.html

---

### `assert_series_equal()`

**What it does:** Assert Series equality in tests.

**Example:**

```python
pd.testing.assert_series_equal(
    actual,
    expected
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.testing.assert_series_equal.html

---

## Inspection, Selection, Indexing, and Structure

### `head()`

**What it does:** Return the first N rows.

**Example:**

```python
df.head()
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.head.html

---

### `tail()`

**What it does:** Return the last N rows.

**Example:**

```python
df.tail()
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.tail.html

---

### `sample()`

**What it does:** Return a random sample of rows or columns.

**Important notes:** Set `random_state=` when reproducibility matters.

**Example:**

```python
df.sample(5)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.sample.html

---

### `info()`

**What it does:** Print structural information, dtypes, null counts, and memory usage.

**Example:**

```python
df.info()
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.info.html

---

### `memory_usage()`

**What it does:** Measure memory consumed by columns and/or index.

**Example:**

```python
df.memory_usage()
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.memory_usage.html

---

### `describe()`

**What it does:** Compute descriptive summary statistics.

**Example:**

```python
df.describe()
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.describe.html

---

### `value_counts()`

**What it does:** Count occurrences of each unique value.

**Example:**

```python
df["city"].value_counts()
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.value_counts.html

---

### `nunique()`

**What it does:** Count distinct values.

**Example:**

```python
df["city"].nunique()
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.nunique.html

---

### `unique()`

**What it does:** Return unique Series values.

**Example:**

```python
df["city"].unique()
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.unique.html

---

### `corr()`

**What it does:** Compute pairwise correlations.

**Example:**

```python
df.corr(
    numeric_only=True
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.corr.html

---

### `select_dtypes()`

**What it does:** Select DataFrame columns by dtype.

**Example:**

```python
df.select_dtypes(
    include="number"
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.select_dtypes.html

---

### `convert_dtypes()`

**What it does:** Convert columns to suitable Pandas extension dtypes.

**Example:**

```python
df = df.convert_dtypes()
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.convert_dtypes.html

---

### `astype()`

**What it does:** Cast Series/DataFrame values to specified dtypes.

**Important notes:** Use nullable Pandas dtypes such as `Int64`, `Float64`, `boolean`, and `string` when missing values must be preserved.

**Example:**

```python
df["age"] = df["age"].astype("int64")
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.astype.html

---

### `set_index()`

**What it does:** Move one or more columns into the index.

**Example:**

```python
df = df.set_index(
    "user_id"
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.set_index.html

---

### `reset_index()`

**What it does:** Move index levels back into columns and restore a default index.

**Example:**

```python
df = df.reset_index()
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.reset_index.html

---

### `reindex()`

**What it does:** Conform data to a new set of labels.

**Example:**

```python
df = df.reindex(
    [100, 200, 300]
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.reindex.html

---

### `sort_index()`

**What it does:** Sort by index labels.

**Example:**

```python
df = df.sort_index()
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.sort_index.html

---

### `xs()`

**What it does:** Select a cross-section from a MultiIndex.

**Example:**

```python
df.xs(
    "Store_A",
    level="store"
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.xs.html

---

### `from_product()`

**What it does:** Construct a MultiIndex from the Cartesian product of iterables.

**Example:**

```python
index = pd.MultiIndex.from_product(
    [
        ["A", "B"],
        [1, 2]
    ],
    names=["group", "id"]
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.MultiIndex.from_product.html

---

### `filter()`

**What it does:** Select labels by item list, substring, or regex.

**Example:**

```python
df.filter(
    like="sales"
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.filter.html

---

### `drop()`

**What it does:** Remove rows or columns by label.

**Example:**

```python
df = df.drop(
    columns=["temporary_column"]
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.drop.html

---

### `pop()`

**What it does:** Remove and return a DataFrame column.

**Example:**

```python
column = df.pop(
    "temporary"
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.pop.html

---

### `insert()`

**What it does:** Insert a column at a specific position.

**Example:**

```python
df.insert(
    0,
    "id",
    range(len(df))
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.insert.html

---

### `rename()`

**What it does:** Rename columns or index labels.

**Example:**

```python
df = df.rename(
    columns={
        "old_name": "new_name"
    }
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.rename.html

---

### `assign()`

**What it does:** Return a DataFrame with added/replaced columns.

**Example:**

```python
df = df.assign(
    age_plus_10=df["age"] + 10,
    is_adult=df["age"] >= 18
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.assign.html

---

### `query()`

**What it does:** Filter rows using a readable expression string.

**Important notes:** Use `@name` to reference Python variables inside the query expression.

**Example:**

```python
df.query(
    "age > 30"
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.query.html

---

### `between()`

**What it does:** Test whether each value falls inside a range.

**Example:**

```python
df[
    df["age"].between(
        25,
        40,
        inclusive="both"
    )
]
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.between.html

---

### `isin()`

**What it does:** Test membership in a collection.

**Example:**

```python
df[
    df["city"].isin(
        ["Chicago", "Minneapolis"]
    )
]
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.isin.html

---

### `sort_values()`

**What it does:** Perform the `sort_values` operation on the relevant Pandas object.

**Example:**

```python
df = df.sort_values(
    "age"
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.sort_values.html

---

### `nlargest()`

**What it does:** Return the largest N values/rows.

**Example:**

```python
df.nlargest(
    10,
    "sales"
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.nlargest.html

---

### `nsmallest()`

**What it does:** Return the smallest N values/rows.

**Example:**

```python
df.nsmallest(
    10,
    "sales"
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.nsmallest.html

---

## Missing Values and Duplicate Handling

### `isna()`

**What it does:** Detect missing values.

**Example:**

```python
df.isna().sum()
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.isna.html

---

### `isnull()`

**What it does:** Alias for isna().

**Example:**

```python
df.isnull()
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.isnull.html

---

### `notna()`

**What it does:** Detect non-missing values.

**Example:**

```python
df[
    df["age"].notna()
]
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.notna.html

---

### `any()`

**What it does:** Test whether any value is true.

**Example:**

```python
df[
    df.isna().any(axis=1)
]
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.any.html

---

### `all()`

**What it does:** Test whether all values are true.

**Example:**

```python
df[
    df.isna().all(axis=1)
]
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.all.html

---

### `dropna()`

**What it does:** Remove rows or columns containing missing values.

**Important notes:** `subset=` limits which columns matter; `how=` controls any/all behavior; `thresh=` sets a minimum count of non-missing values.

**Example:**

```python
df = df.dropna()
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.dropna.html

---

### `fillna()`

**What it does:** Fill missing values.

**Example:**

```python
df["age"] = df["age"].fillna(
    0
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.fillna.html

---

### `ffill()`

**What it does:** Forward-fill missing values from prior observations.

**Example:**

```python
df = df.ffill()
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.ffill.html

---

### `bfill()`

**What it does:** Backward-fill missing values from later observations.

**Example:**

```python
df = df.bfill()
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.bfill.html

---

### `interpolate()`

**What it does:** Estimate missing values using interpolation.

**Example:**

```python
df["value"] = (
    df["value"]
    .interpolate()
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.interpolate.html

---

### `replace()`

**What it does:** Replace values using mappings, literals, lists, or regex patterns.

**Example:**

```python
df.columns = (
    df.columns
    .str.strip()
    .str.lower()
    .str.replace(
        " ",
        "_",
        regex=False
    )
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.replace.html

---

### `duplicated()`

**What it does:** Mark duplicate values or rows.

**Example:**

```python
df.index.duplicated()
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.duplicated.html

---

### `drop_duplicates()`

**What it does:** Remove duplicate rows.

**Example:**

```python
df = df.drop_duplicates()
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.drop_duplicates.html

---

## String Methods

### `Series.str.lower()`

**What it does:** Convert strings to lowercase.

**Example:**

```python
df.columns = (
    df.columns
    .str.strip()
    .str.lower()
    .str.replace(
        " ",
        "_",
        regex=False
    )
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.str.lower.html

---

### `Series.str.upper()`

**What it does:** Convert strings to uppercase.

**Example:**

```python
df["name"] = (
    df["name"]
    .str.upper()
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.str.upper.html

---

### `Series.str.title()`

**What it does:** Convert strings to title case.

**Example:**

```python
df["name"] = (
    df["name"]
    .str.title()
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.str.title.html

---

### `Series.str.strip()`

**What it does:** Trim leading and trailing characters/whitespace.

**Example:**

```python
df.columns = (
    df.columns
    .str.strip()
    .str.lower()
    .str.replace(
        " ",
        "_",
        regex=False
    )
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.str.strip.html

---

### `Series.str.lstrip()`

**What it does:** Trim leading characters/whitespace.

**Example:**

```python
df["name"].str.lstrip()
df["name"].str.rstrip()
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.str.lstrip.html

---

### `Series.str.rstrip()`

**What it does:** Trim trailing characters/whitespace.

**Example:**

```python
df["name"].str.lstrip()
df["name"].str.rstrip()
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.str.rstrip.html

---

### `Series.str.contains()`

**What it does:** Test strings for substring or regex matches.

**Example:**

```python
df[
    df["name"].str.contains(
        "alice",
        case=False,
        na=False
    )
]
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.str.contains.html

---

### `Series.str.startswith()`

**What it does:** Test whether strings begin with a prefix.

**Example:**

```python
df["code"].str.startswith(
    "ABC"
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.str.startswith.html

---

### `Series.str.endswith()`

**What it does:** Test whether strings end with a suffix.

**Example:**

```python
df["file"].str.endswith(
    ".csv"
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.str.endswith.html

---

### `Series.str.split()`

**What it does:** Split strings by a delimiter or regex.

**Example:**

```python
df[
    ["first_name", "last_name"]
] = (
    df["full_name"]
    .str.split(
        " ",
        n=1,
        expand=True
    )
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.str.split.html

---

### `Series.str.extract()`

**What it does:** Extract regex capture groups from strings.

**Example:**

```python
df["domain"] = (
    df["email"]
    .str.extract(
        r"@(.+)$",
        expand=False
    )
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.str.extract.html

---

### `Series.str.zfill()`

**What it does:** Left-pad strings with zeros.

**Example:**

```python
df["id"] = (
    df["id"]
    .str.zfill(8)
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.str.zfill.html

---

### `Series.str.len()`

**What it does:** Return string lengths.

**Example:**

```python
df["name_length"] = (
    df["name"]
    .str.len()
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.str.len.html

---

## Categorical Methods

### `Series.cat.rename_categories()`

**What it does:** Rename category labels.

**Example:**

```python
df["status"] = (
    df["status"]
    .cat.rename_categories({
        "A": "Active",
        "I": "Inactive"
    })
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.cat.rename_categories.html

---

### `Series.cat.add_categories()`

**What it does:** Add allowed labels to a categorical dtype.

**Example:**

```python
df["status"] = (
    df["status"]
    .cat.add_categories(
        ["Unknown"]
    )
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.cat.add_categories.html

---

## Datetime, Timezone, Duration, Offset, and Period Methods

### `Series.dt.strftime()`

**What it does:** Format datetime values as strings.

**Example:**

```python
df["date_text"] = (
    df["timestamp"]
    .dt.strftime(
        "%Y-%m-%d"
    )
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.dt.strftime.html

---

### `Series.dt.day_name()`

**What it does:** Return weekday names.

**Example:**

```python
df["day_name"] = (
    df["timestamp"].dt.day_name()
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.dt.day_name.html

---

### `Series.dt.month_name()`

**What it does:** Return month names.

**Example:**

```python
df["month_name"] = (
    df["timestamp"].dt.month_name()
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.dt.month_name.html

---

### `Series.dt.isocalendar()`

**What it does:** Return ISO year, week, and weekday components.

**Example:**

```python
iso = (
    df["timestamp"]
    .dt.isocalendar()
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.dt.isocalendar.html

---

### `Series.dt.normalize()`

**What it does:** Set datetime time components to midnight.

**Example:**

```python
df["date_floor"] = (
    df["timestamp"]
    .dt.normalize()
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.dt.normalize.html

---

### `Series.dt.floor()`

**What it does:** Round datetime/timedelta values down to a frequency.

**Example:**

```python
df["day"] = (
    df["timestamp"]
    .dt.floor("D")
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.dt.floor.html

---

### `Series.dt.tz_localize()`

**What it does:** Attach/remove timezone metadata without converting the represented local clock time.

**Important notes:** Use on naive datetimes. `ambiguous=` handles repeated fall-back times and `nonexistent=` handles skipped spring-forward times.

**Example:**

```python
df["timestamp"] = (
    df["timestamp"]
    .dt.tz_localize(
        "America/Chicago"
    )
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.dt.tz_localize.html

---

### `Series.dt.tz_convert()`

**What it does:** Convert aware datetimes between timezones while preserving the instant.

**Important notes:** Use only on timezone-aware values. Converting to UTC preserves the instant and changes the displayed wall-clock time.

**Example:**

```python
df["timestamp"] = (
    df["timestamp"]
    .dt.tz_convert(
        "UTC"
    )
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.dt.tz_convert.html

---

### `pd.Timestamp.now()`

**What it does:** Create a Timestamp for the current time.

**Example:**

```python
now_utc = pd.Timestamp.now(
    tz="UTC"
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Timestamp.now.html

---

### `Series.dt.total_seconds()`

**What it does:** Convert Timedelta values to total elapsed seconds.

**Example:**

```python
df["seconds"] = (
    df["duration"]
    .dt.total_seconds()
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.dt.total_seconds.html

---

### `pandas.tseries.offsets.MonthEnd()`

**What it does:** Calendar offset for month-end boundaries.

**Example:**

```python
ts + offsets.MonthEnd(0)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.tseries.offsets.MonthEnd.html

---

### `pandas.tseries.offsets.BDay()`

**What it does:** Calendar offset for business days.

**Example:**

```python
ts + offsets.BDay(1)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.tseries.offsets.BusinessDay.html

---

### `pandas.tseries.offsets.BusinessMonthEnd()`

**What it does:** Calendar offset for business month-end.

**Example:**

```python
ts + offsets.BusinessMonthEnd(0)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.tseries.offsets.BusinessMonthEnd.html

---

### `pandas.tseries.offsets.CustomBusinessDay()`

**What it does:** Business-day offset with custom holidays/weekmask.

**Example:**

```python
custom_bday = offsets.CustomBusinessDay(
    weekmask="Mon Tue Wed Thu Fri",
    holidays=[
        "2026-01-01",
        "2026-12-25"
    ]
)

next_day = ts + custom_bday
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.tseries.offsets.CustomBusinessDay.html

---

### `pandas.tseries.offsets.Week()`

**What it does:** Calendar offset for weekly boundaries.

**Example:**

```python
ts + offsets.Week(
    weekday=0
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.tseries.offsets.Week.html

---

### `Series.dt.to_period()`

**What it does:** Convert datetimes to calendar Period values.

**Example:**

```python
df["month"] = (
    df["timestamp"]
    .dt.to_period("M")
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.dt.to_period.html

---

### `to_timestamp()`

**What it does:** Convert Period values to timestamps.

**Example:**

```python
periods.to_timestamp()
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.PeriodIndex.to_timestamp.html

---

### `between_time()`

**What it does:** Select DatetimeIndex rows within a time-of-day interval.

**Example:**

```python
df = df.set_index(
    "timestamp"
)

business_hours = (
    df.between_time(
        "09:00",
        "17:00"
    )
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.between_time.html

---

### `at_time()`

**What it does:** Select DatetimeIndex rows matching a time of day.

**Example:**

```python
rows_at_noon = (
    df.at_time(
        "12:00"
    )
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.at_time.html

---

### `resample()`

**What it does:** Group time-series data into new frequency bins.

**Important notes:** A DatetimeIndex/PeriodIndex/TimedeltaIndex or `on=` datetime column is required. Carefully choose `label=` and `closed=` at bin boundaries.

**Example:**

```python
daily = (
    df["sales"]
    .resample("D")
    .sum()
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.resample.html

---

### `asfreq()`

**What it does:** Conform time-series data to a new frequency without aggregation.

**Example:**

```python
hourly = (
    daily
    .resample("h")
    .asfreq()
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.asfreq.html

---

## Grouping, Aggregation, and Windows

### `groupby()`

**What it does:** Split data into groups for aggregation, transformation, or filtering.

**Important notes:** `as_index=False` is often convenient when you want grouping keys returned as normal columns.

**Example:**

```python
df.groupby(
    "city"
)["age"].mean()
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.groupby.html

---

### `agg()`

**What it does:** Apply one or more aggregation functions.

**Example:**

```python
daily = (
    df
    .resample("D")
    .agg({
        "sales": "sum",
        "temperature": "mean"
    })
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.core.groupby.DataFrameGroupBy.agg.html

---

### `size()`

**What it does:** Count group rows/elements.

**Example:**

```python
df.groupby(
    "city"
).size()
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.core.groupby.DataFrameGroupBy.size.html

---

### `count()`

**What it does:** Count non-missing observations.

**Example:**

```python
df.groupby(
    "city"
)["age"].count()
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.count.html

---

### `sum()`

**What it does:** Compute sums.

**Example:**

```python
df.memory_usage(
    deep=True
).sum()
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.sum.html

---

### `mean()`

**What it does:** Compute arithmetic mean.

**Example:**

```python
df.isna().mean() * 100
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.mean.html

---

### `median()`

**What it does:** Compute median.

**Example:**

```python
df["age"] = df["age"].fillna(
    df["age"].median()
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.median.html

---

### `transform()`

**What it does:** Return group calculations aligned to original rows.

**Example:**

```python
df["city_avg_age"] = (
    df
    .groupby("city")
    ["age"]
    .transform("mean")
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.core.groupby.SeriesGroupBy.transform.html

---

### `rank()`

**What it does:** Assign ranks to values.

**Example:**

```python
df["rank"] = (
    df
    .groupby("city")
    ["sales"]
    .rank(
        ascending=False
    )
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.rank.html

---

### `rolling()`

**What it does:** Create moving-window calculations over rows or elapsed time.

**Important notes:** A numeric window means rows; an offset such as `'24h'` means elapsed time. Use `min_periods=` to control early results.

**Example:**

```python
df["rolling_mean"] = (
    df["value"]
    .rolling(
        window=7
    )
    .mean()
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.rolling.html

---

### `expanding()`

**What it does:** Create cumulative windows beginning at the first observation.

**Example:**

```python
df["running_mean"] = (
    df["value"]
    .expanding()
    .mean()
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.expanding.html

---

### `ewm()`

**What it does:** Create exponentially weighted window calculations.

**Example:**

```python
df["ewm"] = (
    df["value"]
    .ewm(
        span=10,
        adjust=False
    )
    .mean()
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.ewm.html

---

### `shift()`

**What it does:** Move values forward/backward by periods.

**Example:**

```python
df["previous_sales"] = (
    df["sales"].shift(1)
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.shift.html

---

### `diff()`

**What it does:** Compute differences from prior/later periods.

**Example:**

```python
df["sales_change"] = (
    df["sales"].diff()
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.diff.html

---

### `pct_change()`

**What it does:** Compute fractional change between periods.

**Example:**

```python
df["pct_change"] = (
    df["sales"]
    .pct_change()
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.pct_change.html

---

### `cumsum()`

**What it does:** Compute cumulative sums.

**Example:**

```python
df["running_total"] = (
    df["sales"]
    .cumsum()
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.cumsum.html

---

### `cummax()`

**What it does:** Compute cumulative maxima.

**Example:**

```python
df["running_max"] = (
    df["sales"]
    .cummax()
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.cummax.html

---

## Reshaping and Transformation

### `melt()`

**What it does:** Unpivot wide data into long form.

**Example:**

```python
long = df.melt(
    id_vars="id",
    var_name="month",
    value_name="sales"
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.melt.html

---

### `pivot()`

**What it does:** Reshape unique key combinations from long to wide form.

**Example:**

```python
wide = df.pivot(
    index="id",
    columns="month",
    values="sales"
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.pivot.html

---

### `stack()`

**What it does:** Move column levels into the row index.

**Example:**

```python
stacked = df.stack()
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.stack.html

---

### `unstack()`

**What it does:** Move index levels into columns.

**Example:**

```python
wide = stacked.unstack()
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.unstack.html

---

### `explode()`

**What it does:** Expand list-like cells into multiple rows.

**Example:**

```python
df = pd.DataFrame({
    "id": [1, 2],
    "tags": [
        ["python", "pandas"],
        ["sql"]
    ]
})

exploded = df.explode(
    "tags"
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.explode.html

---

### `map()`

**What it does:** Map Series values through a mapping or function.

**Example:**

```python
mapping = {
    "M": "Male",
    "F": "Female"
}

df["gender_label"] = (
    df["gender"].map(
        mapping
    )
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.map.html

---

### `apply()`

**What it does:** Apply a callable to Series values or DataFrame rows/columns.

**Important notes:** Prefer vectorized arithmetic and `.str`, `.dt`, or `.cat` operations when available; row-wise `axis=1` apply is commonly slower.

**Example:**

```python
df["age_group"] = (
    df["age"]
    .apply(
        lambda x:
        "adult"
        if x >= 18
        else "minor"
    )
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.apply.html

---

### `mul()`

**What it does:** Perform aligned element-wise multiplication.

**Example:**

```python
percent = (
    df["category"]
    .value_counts(
        normalize=True
    )
    .mul(100)
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.mul.html

---

### `to_frame()`

**What it does:** Convert a Series into a one-column DataFrame.

**Example:**

```python
summary = (
    df
    .groupby("category")
    ["sales"]
    .sum()
    .sort_values(
        ascending=False
    )
    .to_frame()
)

summary["share"] = (
    summary["sales"]
    / summary["sales"].sum()
)

# ...
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.Series.to_frame.html

---

### `eval()`

**What it does:** Evaluate an expression against DataFrame columns.

**Example:**

```python
df.query(...)
df.eval(...)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.eval.html

---

## Output, Copying, and Iteration

### `to_csv()`

**What it does:** Write data to CSV/delimited text.

**Important notes:** `index=False` is common for interchange. CSV does not preserve rich Pandas dtype metadata the way Parquet can.

**Example:**

```python
df.to_csv(
    "output.csv",
    index=False
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.to_csv.html

---

### `to_excel()`

**What it does:** Write data to Excel.

**Example:**

```python
df.to_excel(
    "output.xlsx",
    index=False
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.to_excel.html

---

### `to_json()`

**What it does:** Serialize data to JSON.

**Example:**

```python
df.to_json(
    "output.json",
    orient="records",
    date_format="iso"
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.to_json.html

---

### `to_parquet()`

**What it does:** Write typed columnar Parquet data.

**Example:**

```python
df.to_parquet(
    "output.parquet",
    index=False
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.to_parquet.html

---

### `to_pickle()`

**What it does:** Serialize a Pandas object with Python pickle.

**Example:**

```python
df.to_pickle(
    "output.pkl"
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.to_pickle.html

---

### `to_sql()`

**What it does:** Write DataFrame rows to a SQL table.

**Example:**

```python
df.to_sql(
    "table_name",
    connection,
    if_exists="append",
    index=False
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.to_sql.html

---

### `to_clipboard()`

**What it does:** Copy tabular data to the clipboard.

**Example:**

```python
df.to_clipboard(
    index=False
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.to_clipboard.html

---

### `copy()`

**What it does:** Create an explicit copy of a Pandas object.

**Example:**

```python
subset = (
    df.loc[
        df["age"] > 30
    ]
    .copy()
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.copy.html

---

### `iterrows()`

**What it does:** Iterate through DataFrame rows as (index, Series) pairs.

**Example:**

```python
for i, row in df.iterrows():
    df.loc[i, "total"] = (
        row["price"]
        * row["quantity"]
    )
```

**Official documentation:** https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.iterrows.html

---

## Additional Methods Used in the Guide

### `tolist()`

**What it does:** Perform the `tolist` operation on the relevant Pandas object.

**Example:**

```python
df.columns.tolist()
```

**Official documentation:** https://pandas.pydata.org/docs/reference/

---

### `where()`

**What it does:** Perform the `where` operation on the relevant Pandas object.

**Example:**

```python
df["age_group"] = np.where(
    df["age"] >= 18,
    "adult",
    "minor"
)
```

**Official documentation:** https://pandas.pydata.org/docs/reference/

---

# Official Documentation

Recommended official references:

- Pandas documentation  
  https://pandas.pydata.org/docs/

- Pandas user guide  
  https://pandas.pydata.org/docs/user_guide/

- Pandas API reference  
  https://pandas.pydata.org/docs/reference/

- Getting started tutorials  
  https://pandas.pydata.org/docs/getting_started/intro_tutorials/

- Indexing and selecting data  
  https://pandas.pydata.org/docs/user_guide/indexing.html

- Missing data  
  https://pandas.pydata.org/docs/user_guide/missing_data.html

- Working with text data  
  https://pandas.pydata.org/docs/user_guide/text.html

- Categorical data  
  https://pandas.pydata.org/docs/user_guide/categorical.html

- GroupBy  
  https://pandas.pydata.org/docs/user_guide/groupby.html

- Merge, join, and concatenate  
  https://pandas.pydata.org/docs/user_guide/merging.html

- Reshaping and pivot tables  
  https://pandas.pydata.org/docs/user_guide/reshaping.html

- Time series / date functionality  
  https://pandas.pydata.org/docs/user_guide/timeseries.html

- Timedeltas  
  https://pandas.pydata.org/docs/user_guide/timedeltas.html

- Windowing operations  
  https://pandas.pydata.org/docs/user_guide/window.html

- I/O tools  
  https://pandas.pydata.org/docs/user_guide/io.html

- Enhancing performance  
  https://pandas.pydata.org/docs/user_guide/enhancingperf.html

- Scaling to large datasets  
  https://pandas.pydata.org/docs/user_guide/scale.html

- Copy-on-Write  
  https://pandas.pydata.org/docs/user_guide/copy_on_write.html

- Testing utilities  
  https://pandas.pydata.org/docs/reference/testing.html

---

# License

Use, adapt, and extend this cheat sheet for learning, development, and reference.

Contributions and improvements are welcome.
