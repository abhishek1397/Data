## 🧠 MySQL Practice Notes – Pivot by Occupation [Link](https://www.hackerrank.com/challenges/occupations/problem?isFullScreen=true)

## 🗂️ Problem Statement

> Pivot the `Occupation` column in the `OCCUPATIONS` table so that each `Name` is sorted alphabetically and displayed underneath its corresponding `Occupation`.
> The output should consist of four columns in the following order:
>
> * **Doctor**
> * **Professor**
> * **Singer**
> * **Actor**
>   Fill in `NULL` where no name exists.

---

## 🔧 SQL Functions & Techniques Used

| Function / Technique      | Purpose                                                                      |
| ------------------------- | ---------------------------------------------------------------------------- |
| `ROW_NUMBER() OVER (...)` | Assigns a unique row number partitioned by `Occupation` & ordered by `Name`. |
| `CASE WHEN ... THEN ...`  | Used to pivot rows into columns based on `Occupation`.                       |
| `MAX()` + `GROUP BY`      | Aggregates names under each row number to produce a flat result.             |



### 🗂 **Table Used: `OCCUPATIONS`**

Assume this sample data:

| Name  | Occupation |
| ----- | ---------- |
| Eve   | Doctor     |
| Bob   | Professor  |
| Alice | Actor      |
| John  | Singer     |
| Dan   | Actor      |
| Priya | Doctor     |
| Meera | Singer     |
| Jacob | Professor  |

---

## ✅ Step-by-Step Breakdown

---

### **🔹 Step 1: Assign Row Numbers Within Each Occupation**

```sql
SELECT 
    Name,
    Occupation,
    ROW_NUMBER() OVER (PARTITION BY Occupation ORDER BY Name) AS rn
FROM OCCUPATIONS;
```

**What happens:**

* The `ROW_NUMBER()` function assigns a unique number (`rn`) to each person **within their occupation**, ordered by `Name` alphabetically.

**Intermediate Output:**

| Name  | Occupation | rn |
| ----- | ---------- | -- |
| Alice | Actor      | 1  |
| Dan   | Actor      | 2  |
| Eve   | Doctor     | 1  |
| Priya | Doctor     | 2  |
| Bob   | Professor  | 1  |
| Jacob | Professor  | 2  |
| John  | Singer     | 1  |
| Meera | Singer     | 2  |

---

### **🔹 Step 2: Use This as a Subquery to Pivot Names into Columns**

Now we take that result as a subquery (aliased as `Ranked`) and pivot using conditional aggregation:

```sql
SELECT
    MAX(CASE WHEN Occupation = 'Doctor' THEN Name END) AS Doctor,
    MAX(CASE WHEN Occupation = 'Professor' THEN Name END) AS Professor,
    MAX(CASE WHEN Occupation = 'Singer' THEN Name END) AS Singer,
    MAX(CASE WHEN Occupation = 'Actor' THEN Name END) AS Actor
FROM (
    SELECT 
        Name,
        Occupation,
        ROW_NUMBER() OVER (PARTITION BY Occupation ORDER BY Name) AS rn
    FROM OCCUPATIONS
) AS Ranked
GROUP BY rn
ORDER BY rn;
```

---

### **🔹 Step 3: Group by Row Number (`rn`)**

Now we `GROUP BY rn` to align people of the **same rank** across occupations into the same row.

---

### ✅ Final Output:

| Doctor | Professor | Singer | Actor |
| ------ | --------- | ------ | ----- |
| Eve    | Bob       | John   | Alice |
| Priya  | Jacob     | Meera  | Dan   |

* **Row 1**: Everyone with `rn = 1`
* **Row 2**: Everyone with `rn = 2`

---

## 🧠 Summary of Logic:

| Step | Action                                      | Purpose                                                     |
| ---- | ------------------------------------------- | ----------------------------------------------------------- |
| 1    | Assign `ROW_NUMBER()` per occupation        | To track order within each group                            |
| 2    | Use subquery with row numbers               | Prepares data for pivoting                                  |
| 3    | Use `MAX(CASE WHEN...)` for each occupation | Turns rows into columns                                     |
| 4    | `GROUP BY rn`                               | Groups all occupations of same row number into a single row |
| 5    | `ORDER BY rn`                               | Keeps final output in correct order                         |

---

#### Markdown Tip:
 Pivoting Occupation Data using ROW_NUMBER and CASE WHEN
This SQL transforms vertical data into horizontal format by:
- Assigning row numbers per group
- Pivoting using CASE + MAX


# Pandas

Let's solve the same problem using **Pandas in Python**. We’ll replicate the SQL logic step by step using Pandas functions.

### 🧾 Problem Recap:

Given a DataFrame with two columns: `Name` and `Occupation`, pivot the data so that:

* Each **row** contains names of people from **different occupations**, aligned by their **alphabetical order within each occupation**.

---

### ✅ Step-by-Step Pandas Implementation

#### Step 0: Sample Data

```python
import pandas as pd

# Sample data
data = {
    'Name': ['Eve', 'Bob', 'Alice', 'John', 'Dan', 'Priya', 'Meera', 'Jacob'],
    'Occupation': ['Doctor', 'Professor', 'Actor', 'Singer', 'Actor', 'Doctor', 'Singer', 'Professor']
}

df = pd.DataFrame(data)
```

---

#### Step 1: Assign Row Numbers Within Each Occupation (like `ROW_NUMBER()`)

```python
df['rn'] = df.groupby('Occupation')['Name'].transform(lambda x: x.sort_values().rank(method='first')).astype(int)
```

**Explanation:**

* `groupby('Occupation')`: groups data by occupation.
* `sort_values().rank(method='first')`: ranks names alphabetically within each occupation.
* `astype(int)`: convert the rank to integer (like SQL `ROW_NUMBER()`).

---

#### Step 2: Pivot the Table

```python
pivot_df = df.pivot(index='rn', columns='Occupation', values='Name')
```

---

#### Step 3: Optional - Sort by Row Number and Clean Output

```python
pivot_df = pivot_df.sort_index().reset_index(drop=True)
```

---

### ✅ Final Output

```python
print(pivot_df)
```

You get:

```
Occupation   Actor Doctor Professor Singer
0            Alice   Eve       Bob   John
1              Dan Priya    Jacob Meera
```

---

### 🔁 Full Code Block

```python
import pandas as pd

# Step 0: Original DataFrame
data = {
    'Name': ['Eve', 'Bob', 'Alice', 'John', 'Dan', 'Priya', 'Meera', 'Jacob'],
    'Occupation': ['Doctor', 'Professor', 'Actor', 'Singer', 'Actor', 'Doctor', 'Singer', 'Professor']
}
df = pd.DataFrame(data)

# Step 1: Assign row number within each occupation
df['rn'] = df.groupby('Occupation')['Name'].transform(lambda x: x.sort_values().rank(method='first')).astype(int)

# Step 2: Pivot
pivot_df = df.pivot(index='rn', columns='Occupation', values='Name')

# Step 3: Sort and clean
pivot_df = pivot_df.sort_index().reset_index(drop=True)

print(pivot_df)
```

---

