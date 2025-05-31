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

---

## 🧠 Key SQL Concept: PIVOT Without Native Support

MySQL doesn’t support `PIVOT` directly, so we simulate it by:

1. Ranking rows within each group using `ROW_NUMBER()`.
2. Assigning names to columns using `CASE` inside a `MAX()` aggregation.

---

## 💡 Example Solution

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
) AS ranked
GROUP BY rn;
```

---

## 🧪 Sample Output Format

| Doctor | Professor | Singer   | Actor |
| ------ | --------- | -------- | ----- |
| Amy    | Ashley    | Jane     | Ayan  |
| NULL   | Christeen | Jenny    | Julia |
| NULL   | NULL      | Kristeen | Maria |

---

## 📌 Notes

* `ROW_NUMBER()` is available in MySQL 8.0+. Use manual counters or variables for older versions.
* `MAX(...)` ensures a single value per group when pivoting.

---

=====================================================================================================
