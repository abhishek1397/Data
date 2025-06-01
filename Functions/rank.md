# Rank()

Let's go over the `rank()` function in **Pandas**, which is similar to `RANK()` or `ROW_NUMBER()` in SQL.

---

## 🧠 What is `rank()` in Pandas?

The `rank()` function assigns **ranks** to elements in a Series. It's often used in **sorting, grouping, or scoring** data.

---

## ✅ Basic Syntax:

```python
Series.rank(method='average', ascending=True)
```

### 🔸 Key Parameters:

* **`method`**: How to rank ties. Options:

  * `'average'`: Average of the ranks for tied values (default).
  * `'min'`: Lowest rank in the group.
  * `'max'`: Highest rank in the group.
  * `'first'`: Ranks in order of appearance.
  * `'dense'`: Like `min`, but no gaps in ranks.
* **`ascending=True/False`**: Controls sort order.

---

## 📘 Example 1: Simple Ranking

```python
import pandas as pd

scores = pd.Series([90, 85, 90, 70])

print(scores.rank())
```

### Output:

```
0    3.5
1    2.0
2    3.5
3    1.0
dtype: float64
```

**Explanation**:

* The two 90s are tied, so they both get an average rank: (3 + 4)/2 = 3.5
* 85 gets rank 2
* 70 gets rank 1

---

## 📘 Example 2: Using `method='first'` (like SQL `ROW_NUMBER()`)

```python
print(scores.rank(method='first'))
```

### Output:

```
0    3.0
1    2.0
2    4.0
3    1.0
```

**Explanation**:

* Ranks are assigned based on order of appearance.
* Even if values are tied (like 90), the first occurrence gets the lower rank.

---

## 📘 Example 3: Ranking Within Groups (like `ROW_NUMBER()` with `PARTITION BY`)

```python
df = pd.DataFrame({
    'Name': ['Alice', 'Bob', 'Charlie', 'David', 'Eve', 'Frank'],
    'Department': ['HR', 'HR', 'IT', 'IT', 'IT', 'HR'],
    'Score': [95, 88, 92, 92, 85, 75]
})

df['rank'] = df.groupby('Department')['Score'].rank(method='first', ascending=False)
print(df)
```

### Output:

| Name    | Department | Score | rank |
| ------- | ---------- | ----- | ---- |
| Alice   | HR         | 95    | 1.0  |
| Bob     | HR         | 88    | 2.0  |
| Frank   | HR         | 75    | 3.0  |
| Charlie | IT         | 92    | 1.0  |
| David   | IT         | 92    | 2.0  |
| Eve     | IT         | 85    | 3.0  |

**Explanation**:

* For each department, ranks are assigned in descending order of score.
* Ties are broken by order of appearance (`method='first'`).

---

## ✅ Summary Table of `method` Options:

| Method    | Description                                         |
| --------- | --------------------------------------------------- |
| `average` | Average of ranks for ties                           |
| `min`     | Lowest possible rank for ties                       |
| `max`     | Highest possible rank for ties                      |
| `first`   | Assign ranks in the order they appear in the Series |
| `dense`   | Like min, but no gaps in ranks                      |

---

# Sorting

```python
pivot_df = pivot_df.sort_index().reset_index(drop=True)
```

This line is **cleaning and preparing your pivoted DataFrame**, especially after using `pivot()`.

---

## 🔍 Step-by-Step Explanation

### 1️⃣ `pivot_df.sort_index()`

* This **sorts the rows** of the DataFrame by the **row index** (usually integers).
* If you've used `pivot()` with an index like `rn` (row number), sorting ensures that the rows appear in the correct order (e.g., row 1, 2, 3...).

---

### 2️⃣ `.reset_index(drop=True)`

* Resets the row index to default `0, 1, 2, ...`
* **`drop=True`**: prevents the old index from becoming a new column.

---

## 📘 Example:

Let’s say after pivoting, you have this:

```python
import pandas as pd

data = {
    'Doctor': {2: 'Priya', 1: 'Eve'},
    'Professor': {1: 'Bob', 2: 'Jacob'},
    'Singer': {1: 'John', 2: 'Meera'},
    'Actor': {1: 'Alice', 2: 'Dan'}
}

pivot_df = pd.DataFrame(data)
print(pivot_df)
```

### Output before cleaning:

```
   Doctor Professor Singer  Actor
2  Priya    Jacob  Meera    Dan
1    Eve      Bob   John  Alice
```

Notice the **index is out of order** (2, then 1).

---

### Apply the cleanup:

```python
pivot_df = pivot_df.sort_index().reset_index(drop=True)
print(pivot_df)
```

### ✅ Output after sorting and resetting index:

```
  Doctor Professor Singer  Actor
0    Eve       Bob   John  Alice
1  Priya     Jacob  Meera    Dan
```

* Rows are now in the correct order (based on `rn`)
* Index is reset to `0, 1` instead of `2, 1`

---

## ✅ Why Use This?

When you pivot or group data:

* The **row index might not be sorted**
* It might retain unwanted index values (like `rn`, `group labels`, etc.)

Using `sort_index().reset_index(drop=True)` ensures:

* Clean, readable, correctly ordered DataFrame
* Good for exporting, plotting, or displaying

---

