# Tree Structure Classification 

**Tree Structure Classification** problem where we analyze a **parent-child relationship** within a binary tree to determine each node's type:
[Link](https://www.hackerrank.com/challenges/binary-search-tree-1/problem?isFullScreen=true)

![image](https://github.com/user-attachments/assets/3b4aecfe-3a6c-4270-82bd-c5c7a476ef44)

---

### 🧠 **Understanding the Problem**

You are given a table `BST` with two columns:

| N | P    |
| - | ---- |
| 1 | 2    |
| 3 | 2    |
| 6 | 8    |
| 9 | 8    |
| 2 | 5    |
| 8 | 5    |
| 5 | NULL |

Where:

* `N` is a **node**.
* `P` is its **parent**.
* `P = NULL` means the node is the **root**.

---

### ✅ **Goal**

Output each node's type:

* `"Root"`: if it's **not a child of any node** (i.e., `P IS NULL`).
* `"Leaf"`: if it's **not a parent** of any node.
* `"Inner"`: if it's both a **child** and a **parent**.

---

### ✅ Step-by-Step Solution

#### Step 1: Identify Root Node

A node is a **root** if `P IS NULL`.

#### Step 2: Identify Inner Nodes

A node is an **inner node** if it appears in column `P` (i.e., has children).

#### Step 3: Identify Leaf Nodes

A node is a **leaf** if it’s **not a parent** and **not a root**.

---

### ✅ Final SQL Query

```sql
SELECT 
    N,
    CASE 
        WHEN P IS NULL THEN 'Root'
        WHEN N NOT IN (SELECT DISTINCT P FROM BST WHERE P IS NOT NULL) THEN 'Leaf'
        ELSE 'Inner'
    END AS NodeType
FROM BST
ORDER BY N;
```

---

### 🧪 Sample Output

Given:

| N | P    |
| - | ---- |
| 1 | 2    |
| 3 | 2    |
| 6 | 8    |
| 9 | 8    |
| 2 | 5    |
| 8 | 5    |
| 5 | NULL |

Output:

| N | NodeType |
| - | -------- |
| 1 | Leaf     |
| 2 | Inner    |
| 3 | Leaf     |
| 5 | Root     |
| 6 | Leaf     |
| 8 | Inner    |
| 9 | Leaf     |

# **Binary Tree Node Type Classification** problem using **pandas**

Perfect! Let’s now solve the **Binary Tree Node Type Classification** problem using **pandas**, step-by-step, and compare each operation with the SQL version.

---

## 🐼 **Python (Pandas) Solution**

### ✅ Step 1: Import and Load Data

```python
import pandas as pd

# Sample data
data = [
    {'N': 1, 'P': 2},
    {'N': 3, 'P': 2},
    {'N': 6, 'P': 8},
    {'N': 9, 'P': 8},
    {'N': 2, 'P': 5},
    {'N': 8, 'P': 5},
    {'N': 5, 'P': None},
]

df = pd.DataFrame(data)
```

---

### ✅ Step 2: Identify All Parent Nodes

```python
parent_nodes = df['P'].dropna().unique()
```

> **SQL equivalent**: `SELECT DISTINCT P FROM BST WHERE P IS NOT NULL;`

---

### ✅ Step 3: Create Node Type Column

```python
def get_node_type(row):
    if pd.isna(row['P']):
        return 'Root'
    elif row['N'] not in parent_nodes:
        return 'Leaf'
    else:
        return 'Inner'

df['NodeType'] = df.apply(get_node_type, axis=1)
```

> **SQL equivalent**:

```sql
CASE 
  WHEN P IS NULL THEN 'Root'
  WHEN N NOT IN (SELECT DISTINCT P FROM BST WHERE P IS NOT NULL) THEN 'Leaf'
  ELSE 'Inner'
END
```

---

> Note:

```plaintext
- df.apply(...):
    This is a pandas function used to apply a function across either rows or columns of a DataFrame.
- axis=1:
    This tells pandas to apply the function row-wise (i.e., each row is passed to get_node_type()).
    axis=0 would mean column-wise, which we don't want here.
```
---

### ✅ Step 4: Drop Duplicates and Sort

Since the node can appear multiple times (as child in one row, as parent in another), we drop duplicates by column `'N'`.

```python
df_final = df[['N', 'NodeType']].drop_duplicates().sort_values(by='N')
```

---

### ✅ Step 5: Display Output

```python
print(df_final.to_string(index=False))
```

---

### 🧪 Output

```plaintext
 N NodeType
 1     Leaf
 2    Inner
 3     Leaf
 5     Root
 6     Leaf
 8    Inner
 9     Leaf
```

---

### 🔁 Comparison Summary Table

| Step              | SQL                                               | Pandas                            |
| ----------------- | ------------------------------------------------- | --------------------------------- |
| Load data         | `SELECT * FROM BST;`                              | `pd.DataFrame(data)`              |
| Identify parents  | `SELECT DISTINCT P FROM BST WHERE P IS NOT NULL;` | `df['P'].dropna().unique()`       |
| Assign node types | `CASE WHEN ...`                                   | `df.apply(get_node_type, axis=1)` |
| Deduplicate       | Not needed (data normalized)                      | `df.drop_duplicates(['N'])`       |
| Order by N        | `ORDER BY N`                                      | `.sort_values(by='N')`            |

---


