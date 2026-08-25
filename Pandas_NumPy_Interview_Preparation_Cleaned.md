# Pandas + NumPy — Interview Preparation

## 0. First: What are Pandas and NumPy?

### NumPy

> **NumPy is used for numerical computation and works primarily with multidimensional arrays.**

Think:

```
NumPy → numbers / arrays / mathematical operations
```

### Pandas

> **Pandas is used for working with structured/tabular data.**

Think:

```
Pandas → rows / columns / DataFrames / data cleaning / analysis
```

Simple distinction:

> **NumPy = numerical data**
>
> **Pandas = tabular data**

---

# PART 1 — Pandas

## 1. What is a DataFrame?

A DataFrame is essentially a **table with rows and columns**.

Example:

```
import pandas as pd

df = pd.DataFrame({
    "name": ["A", "B", "C"],
    "age": [22, 27, 30],
    "salary": [30000, 50000, 60000]
})
```

Think:

| nameagesalary |    |       |
| ------------- | -- | ----- |
| A             | 22 | 30000 |
| B             | 27 | 50000 |
| C             | 30 | 60000 |

### Interview answer

> "A Pandas DataFrame is a two-dimensional labeled data structure consisting of rows and columns."

That's enough.

---

# 2. `df.head()`

```
df.head()
```

Shows the first 5 rows.

You can specify:

```
df.head(10)
```

### Interview answer

> "I use `head()` to quickly inspect the first few records of a DataFrame."

---

# 3. `df.info()`

```
df.info()
```

Useful for understanding:

-  Number of rows
-  Column names
-  Data types
-  Non-null counts

Example:

```
name       object
age         int64
salary      float64
```

### Interview answer

> "`info()` gives a structural overview of the DataFrame, including column names, data types and non-null counts."

---

# 4. `df.describe()`

```
df.describe()
```

Gives statistical summaries for numerical columns.

Typically:

```
count
mean
std
min
25%
50%
75%
max
```

### Interview answer

> "`describe()` gives summary statistics for numerical columns, which helps me quickly understand distributions and identify unusual values."

---

# 5. `df.shape`

```
df.shape
```

Returns:

```
(rows, columns)
```

Example:

```
(1000, 5)
```

means:

> 1000 rows and 5 columns.

### Remember:

**shape → how big is my data?**

---

# 6. `df.columns`

```
df.columns
```

Returns the column names.

Example:

```
Index(['name', 'age', 'salary'], dtype='object')
```

Useful when you don't remember the exact column names.

---

# 7. `df.dtypes`

```
df.dtypes
```

Shows the data type of every column.

Example:

```
age         int64
salary    float64
name       object
```

---

# 8. Selecting one column

```
df["age"]
```

Returns a Pandas **Series**.

Think:

```
DataFrame → table
Series    → one column
```

---

# 9. Selecting multiple columns

```
df[["name", "salary"]]
```

Notice the **double brackets**.

Why?

Outer `[]` → DataFrame selection.

Inner `[]` → list of column names.

---

# 10. `loc` vs `iloc`

This is a very common interview question.

## `loc`

Think:

> **label-based selection**

```
df.loc[2, "age"]
```

Means:

> Row label 2, column `"age"`.

You can also filter:

```
df.loc[df["age"] > 25, ["name", "age"]]
```

---

## `iloc`

Think:

> **position-based selection**

```
df.iloc[2, 1]
```

Means:

> Third row, second column.

Because indexing starts at 0.

### Memory trick

> **loc → location/label**
>
> **iloc → integer location**

---

# 11. Filtering

This is extremely important.

```
df[df["age"] > 25]
```

Means:

> Give me rows where age is greater than 25.

Multiple conditions:

```
df[(df["age"] > 25) & (df["salary"] > 40000)]
```

OR:

```
df[(df["age"] > 25) | (df["salary"] > 40000)]
```

### Important

Use:

```
&
|
```

rather than Python's normal:

```
and
or
```

for Pandas Series conditions.

---

# 12. Missing values

Suppose:

| nameagesalary |     |       |
| ------------- | --- | ----- |
| A             | 22  | 30000 |
| B             | NaN | 50000 |
| C             | 30  | NaN   |

## Find missing values

```
df.isnull()
```

Gives True/False.

Usually you want:

```
df.isnull().sum()
```

This tells you:

> How many missing values does each column have?

### Interview answer

> "I would first identify the amount and pattern of missing data before deciding how to handle it."

That last part is important.

Don't automatically delete everything.

---

# 13. `dropna()`

```
df.dropna()
```

Removes rows containing missing values.

You can also specify:

```
df.dropna(subset=["age"])
```

Meaning:

> Remove rows where age is missing.

---

# 14. `fillna()`

Instead of removing missing values:

```
df["age"].fillna(df["age"].median())
```

This replaces missing age values with the median.

Common choices:

```
Mean
Median
Mode
Constant value
Forward fill
Backward fill
```

### Interview question:

> "Would you always use mean?"

No.

Say:

> "It depends on the data distribution and why values are missing. For a skewed numerical variable, median may be more appropriate than mean."

Good.

---

# 15. Duplicates

Check duplicates:

```
df.duplicated()
```

Count them:

```
df.duplicated().sum()
```

Remove them:

```
df.drop_duplicates()
```

### Interview answer

> "`duplicated()` identifies duplicate rows, while `drop_duplicates()` removes them."

---

# 16. GroupBy — VERY IMPORTANT

This is one of the most likely practical tasks.

Suppose:

| categorysales |     |
| ------------- | --- |
| A             | 100 |
| B             | 200 |
| A             | 300 |
| B             | 400 |

Question:

> "Find total sales by category."

```
df.groupby("category")["sales"].sum()
```

Result:

```
A → 400
B → 600
```

### Understand what is happening

```
groupby("category")
        ↓
create groups
        ↓
select sales
        ↓
sum
```

---

# 17. GroupBy with multiple aggregations

```
df.groupby("category")["sales"].agg(["sum", "mean", "max"])
```

This gives:

-  Total sales
-  Average sales
-  Maximum sales

You can also do:

```
df.groupby("category").agg({
    "sales": "sum",
    "age": "mean"
})
```

Don't over-memorize the syntax.

Understand:

> **Group → choose metric → aggregate**

---

# 18. Sorting

```
df.sort_values("sales")
```

Ascending by default.

Descending:

```
df.sort_values("sales", ascending=False)
```

### Interview question

> "Give me the top 5 customers by sales."

```
df.sort_values("sales", ascending=False).head(5)
```

This is a very useful pattern.

---

# 19. Merge

Suppose:

### Customers

| idname |   |
| ------ | - |
| 1      | A |
| 2      | B |

### Orders

| idamount |     |
| -------- | --- |
| 1        | 500 |
| 2        | 800 |

Merge:

```
pd.merge(customers, orders, on="id")
```

Now you have:

| idnameamount |   |     |
| ------------ | - | --- |
| 1            | A | 500 |
| 2            | B | 800 |

---

# 20. Joins — know the idea

You don't need to memorize complicated SQL-style diagrams.

Understand:

## Inner join

> Keep only matching records from both tables.

```
pd.merge(df1, df2, on="id", how="inner")
```

---

## Left join

> Keep everything from the left table, matching data from the right where available.

```
pd.merge(df1, df2, on="id", how="left")
```

---

## Right join

> Keep everything from the right table.

```
pd.merge(df1, df2, on="id", how="right")
```

---

## Outer join

> Keep everything from both tables.

```
pd.merge(df1, df2, on="id", how="outer")
```

### Memory trick

```
inner → intersection
left  → all left
right → all right
outer → everything
```

---

# 21. A very common practical interview task

Imagine they give:

```
df
```

and ask:

> "Find the average salary of employees in each department."

You should immediately think:

```
df.groupby("department")["salary"].mean()
```

Then:

> "Find departments with average salary above 50,000."

```
result = df.groupby("department")["salary"].mean()

result[result > 50000]
```

This is the level of practical fluency you want.

---

# 22. Another common task

> Find the highest-selling product.

```
df.groupby("product")["sales"].sum().sort_values(ascending=False).head(1)
```

Don't worry if this looks complicated.

Read it from left to right:

```
group products
      ↓
sum sales
      ↓
sort descending
      ↓
take first
```

---

# 23. Your Pandas mental map

Don't memorize 30 functions separately.

Remember this:

```
INSPECT
↓
head()
info()
describe()
shape

SELECT
↓
[]
loc
iloc

FILTER
↓
df[condition]

CLEAN
↓
isnull()
dropna()
fillna()
drop_duplicates()

ANALYZE
↓
groupby()
sum()
mean()
count()

ORDER
↓
sort_values()

COMBINE
↓
merge()
```

That's enough for a huge portion of basic interview questions.

---

# PART 2 — NumPy

Now simplify NumPy in your head.

> **NumPy = arrays + numerical operations**

---

# 24. NumPy array

```
import numpy as np

arr = np.array([1, 2, 3, 4])
```

Unlike a normal Python list, NumPy arrays are designed for efficient numerical computation.

---

# 25. Shape

```
arr.shape
```

For:

```
[1, 2, 3, 4]
```

you get:

```
(4,)
```

For:

```
np.array([
    [1, 2],
    [3, 4],
    [5, 6]
])
```

shape:

```
(3, 2)
```

Meaning:

> 3 rows, 2 columns.

### Remember:

**shape tells you the dimensions of the array.**

---

# 26. Reshape

Suppose:

```
arr = np.array([1, 2, 3, 4, 5, 6])
```

You can do:

```
arr.reshape(2, 3)
```

Result:

```
1 2 3
4 5 6
```

Important:

The total number of elements must remain the same.

```
6 elements

2 × 3 = 6 ✓
```

But:

```
4 × 2 = 8 ✗
```

---

# 27. Indexing

Similar to Python lists:

```
arr[0]
```

First element.

For a 2D array:

```
arr[1, 0]
```

Second row, first column.

---

# 28. Broadcasting

This sounds complicated but isn't.

Broadcasting means NumPy can perform operations between arrays of compatible shapes without manually repeating values.

Example:

```
arr = np.array([1, 2, 3])
arr + 10
```

Result:

```
[11, 12, 13]
```

Conceptually:

```
[1, 2, 3]
+
[10,10,10]
```

NumPy handles this automatically.

### Interview answer

> "Broadcasting allows NumPy to perform operations on arrays with compatible shapes without explicitly creating matching copies."

That's enough.

---

# 29. Vectorization

Instead of:

```
result = []

for x in arr:
    result.append(x * 2)
```

NumPy lets you do:

```
result = arr * 2
```

This is vectorized computation.

### Interview answer

> "Vectorization means performing operations on entire arrays rather than explicitly looping through individual elements, which is generally more efficient in NumPy."

---

# 30. Mean

```
np.mean(arr)
```

Average.

Example:

```
[10, 20, 30]

mean = 20
```

---

# 31. Median

```
np.median(arr)
```

Middle value after sorting.

Example:

```
[10, 20, 30]

median = 20
```

Why useful?

Median is less affected by extreme values.

Example:

```
10, 20, 30, 1000
```

Mean gets heavily affected by 1000.

Median is more representative of the middle.

---

# 32. Standard deviation

```
np.std(arr)
```

Measures how spread out the values are around the mean.

Simple intuition:

```
Low std
→ values close together

High std
→ values spread out
```

Interview answer:

> "Standard deviation measures the dispersion of values around the mean."

That's enough.

---

# 33. Min and Max

```
np.min(arr)
np.max(arr)
```

Straightforward.

---

# 34. `argmax()`

This is slightly different.

```
arr = np.array([10, 50, 30])

np.argmax(arr)
```

Returns:

```
1
```

Why?

Because:

```
index 0 → 10
index 1 → 50 ← maximum
index 2 → 30
```

### Important distinction

```
np.max(arr)
```

returns:

```
50
```

while:

```
np.argmax(arr)
```

returns:

```
1
```

### Memory trick:

> **max → value**
>
> **argmax → position**

---

# 35. Matrix operations

Suppose:

```
A = np.array([
    [1, 2],
    [3, 4]
])

B = np.array([
    [5, 6],
    [7, 8]
])
```

You can perform:

### Element-wise multiplication

```
A * B
```

Each corresponding element is multiplied.

---

### Matrix multiplication

```
A @ B
```

or:

```
np.matmul(A, B)
```

This follows actual matrix multiplication rules.

### Interview distinction

> `*` → element-wise multiplication
>
> `@` → matrix multiplication

This is worth remembering.

---

# 36. Why NumPy matters for ML

This is a good interview question.

> "Why do you need NumPy if you're using Pandas?"

Answer:

> "Pandas is convenient for structured and labeled data, while NumPy provides efficient numerical array operations. Many machine learning computations ultimately involve numerical arrays, so NumPy is useful for mathematical operations and preprocessing."

---

# 37. Pandas vs NumPy — memorize this

| PandasNumPy   |                              |
| ------------- | ---------------------------- |
| Tabular data  | Numerical arrays             |
| DataFrame     | ndarray                      |
| Rows/columns  | Dimensions                   |
| Data cleaning | Mathematical operations      |
| GroupBy       | Array operations             |
| Merge         | Matrix/numerical computation |

Simple interview answer:

> "I generally use Pandas for structured datasets and data manipulation, and NumPy for efficient numerical and array-based computation."

---

# 38. What you actually need to remember

Don't memorize everything in the giant list.

### Pandas — MUST KNOW

```
pd.DataFrame()

df.head()
df.info()
df.describe()
df.shape

df["col"]
df[["a", "b"]]

df.loc[]
df.iloc[]

df[condition]

df.isnull().sum()
df.dropna()
df.fillna()

df.duplicated()
df.drop_duplicates()

df.groupby()
df.sort_values()

pd.merge()
```

### NumPy — MUST KNOW

```
np.array()

arr.shape
arr.reshape()

arr[index]

np.mean()
np.median()
np.std()
np.min()
np.max()
np.argmax()

array + number
array * number

A * B
A @ B
```

And concepts:

```
Broadcasting
Vectorization
```

---

# 39. The 10 things I want you to be able to explain verbally

If I were interviewing you, I'd expect instant answers to:

### 1. DataFrame?

> A two-dimensional labeled table.

### 2. Series?

> A one-dimensional labeled data structure, often representing one DataFrame column.

### 3. `loc` vs `iloc`?

> `loc` uses labels; `iloc` uses integer positions.

### 4. `groupby()`?

> Groups rows based on one or more columns so we can perform aggregations within each group.

### 5. `merge()`?

> Combines DataFrames using common key columns.

### 6. `dropna()`?

> Removes rows/columns containing missing values depending on configuration.

### 7. `fillna()`?

> Replaces missing values with specified values or statistics.

### 8. NumPy?

> Numerical computing library based around multidimensional arrays.

### 9. Broadcasting?

> Applying operations between compatible arrays of different shapes without explicitly copying values.

### 10. Vectorization?

> Performing operations on whole arrays rather than explicit Python loops.

---

# 40. One practical exercise you absolutely should practice

Given:

```
df = pd.DataFrame({
    "department": ["IT", "IT", "HR", "HR", "Sales"],
    "employee": ["A", "B", "C", "D", "E"],
    "salary": [50000, 60000, 45000, 55000, 70000],
    "age": [23, 28, 25, 32, 30]
})
```

If interviewer asks:

### "Find employees older than 25."

```
df[df["age"] > 25]
```

### "Find average salary by department."

```
df.groupby("department")["salary"].mean()
```

### "Find the highest salary."

```
df["salary"].max()
```

### "Find the employee with highest salary."

```
df.loc[df["salary"].idxmax()]
```

That last one is useful.

Remember:

```
max()    → highest value
idxmax() → index of highest value
```

---

# 41. The interview mindset

If they give you a Pandas task, don't panic about syntax.

First say internally:

> **What is the operation?**

Then map it:

```
Look at data       → head/info/describe
Select             → [] / loc / iloc
Filter             → condition
Missing data       → isnull/dropna/fillna
Duplicates         → duplicated/drop_duplicates
Group              → groupby
Calculate          → sum/mean/count
Sort               → sort_values
Combine tables     → merge
```

For NumPy:

```
Create array       → np.array
Dimensions         → shape
Change dimensions  → reshape
Statistics         → mean/median/std
Find extremes      → min/max
Find position      → argmax
Array computation  → vectorization
Different shapes   → broadcasting
Matrix multiplication → @
```

That is **far easier to retain** than trying to memorize a Pandas textbook.
