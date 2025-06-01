# Rank() Pandas

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


# SQL ranking functions and their Pandas equivalents

Let's walk through **different SQL ranking functions** and their **Pandas equivalents**, step by step. We’ll use the same sample data and show how to:

1. Use each SQL ranking function (`ROW_NUMBER`, `RANK`, `DENSE_RANK`)
2. Translate it into **Pandas**
3. Show and explain the output

---

## 🧾 Sample Data

Let’s use this table:

| Name    | Department | Score |
| ------- | ---------- | ----- |
| Alice   | HR         | 95    |
| Bob     | HR         | 88    |
| Frank   | HR         | 88    |
| Charlie | IT         | 92    |
| David   | IT         | 92    |
| Eve     | IT         | 85    |

```python
import pandas as pd

# Sample DataFrame
df = pd.DataFrame({
    'Name': ['Alice', 'Bob', 'Frank', 'Charlie', 'David', 'Eve'],
    'Department': ['HR', 'HR', 'HR', 'IT', 'IT', 'IT'],
    'Score': [95, 88, 88, 92, 92, 85]
})
```

---

## 1️⃣ `ROW_NUMBER()` (SQL) → `rank(method='first')` (Pandas)

### 💡 SQL:

```sql
ROW_NUMBER() OVER (PARTITION BY Department ORDER BY Score DESC)
```

### ✅ Pandas Equivalent:

```python
df['row_number'] = df.groupby('Department')['Score'].rank(method='first', ascending=False).astype(int)
```

**Explanation**:

* Ranks scores **within each department**
* Assigns **unique, sequential** numbers (even for ties, order matters)

### 🧾 Output:

| Name    | Dept | Score | row\_number |
| ------- | ---- | ----- | ----------- |
| Alice   | HR   | 95    | 1           |
| Bob     | HR   | 88    | 2           |
| Frank   | HR   | 88    | 3           |
| Charlie | IT   | 92    | 1           |
| David   | IT   | 92    | 2           |
| Eve     | IT   | 85    | 3           |

---

## 2️⃣ `RANK()` (SQL) → `rank(method='min')` (Pandas)

### 💡 SQL:

```sql
RANK() OVER (PARTITION BY Department ORDER BY Score DESC)
```

### ✅ Pandas Equivalent:

```python
df['rank'] = df.groupby('Department')['Score'].rank(method='min', ascending=False).astype(int)
```

**Explanation**:

* Same scores get **same rank**
* **Gaps** appear in ranking if there are ties

### 🧾 Output:

| Name    | Dept | Score | rank |
| ------- | ---- | ----- | ---- |
| Alice   | HR   | 95    | 1    |
| Bob     | HR   | 88    | 2    |
| Frank   | HR   | 88    | 2    |
| Charlie | IT   | 92    | 1    |
| David   | IT   | 92    | 1    |
| Eve     | IT   | 85    | 3    |

---

## 3️⃣ `DENSE_RANK()` (SQL) → `rank(method='dense')` (Pandas)

### 💡 SQL:

```sql
DENSE_RANK() OVER (PARTITION BY Department ORDER BY Score DESC)
```

### ✅ Pandas Equivalent:

```python
df['dense_rank'] = df.groupby('Department')['Score'].rank(method='dense', ascending=False).astype(int)
```

**Explanation**:

* Same as `RANK`, but **no gaps** in ranks

### 🧾 Output:

| Name    | Dept | Score | dense\_rank |
| ------- | ---- | ----- | ----------- |
| Alice   | HR   | 95    | 1           |
| Bob     | HR   | 88    | 2           |
| Frank   | HR   | 88    | 2           |
| Charlie | IT   | 92    | 1           |
| David   | IT   | 92    | 1           |
| Eve     | IT   | 85    | 2           |

---

## ✅ Summary Table

| SQL Function   | Pandas Method          | Handles Ties | Gaps in Ranks? |
| -------------- | ---------------------- | ------------ | -------------- |
| `ROW_NUMBER()` | `rank(method='first')` | ❌ No         | ❌ No           |
| `RANK()`       | `rank(method='min')`   | ✅ Yes        | ✅ Yes          |
| `DENSE_RANK()` | `rank(method='dense')` | ✅ Yes        | ❌ No           |

---


