# SQLGlot Optimizer Test Report

This report tests SQLGlot's optimizer with various join combinations in PostgreSQL dialect.

## Test Case 1: FULL OUTER (outer) + FULL OUTER (inner)
### Original Query
```sql
SELECT "A".col1, "A".col2 
FROM table_a "A" 
FULL OUTER JOIN (SELECT "B".col1, "B".col2 
                       FROM table_b "B" 
                       FULL OUTER JOIN table_c "C" 
                         ON "B".col2 = "C".col2) "D" 
                   ON "A".col1 = "D".col1
```
### Optimized Query
```sql
WITH D AS (
  SELECT
    "B".col1 AS col1
  FROM table_b AS "B"
  FULL JOIN table_c AS "C"
    ON "B".col2 = "C".col2
)
SELECT
  "A".col1 AS col1,
  "A".col2 AS col2
FROM table_a AS "A"
FULL JOIN D AS D
  ON "A".col1 = "D".col1
```
**Original Row Count**: 9

**Optimized Row Count**: 0

**Match**: NO - POTENTIAL BUG

#### Original Results
| col1 | col2 |
|------|------|
| 1 | a1 |
| 2 | a2 |
| 3 | a3 |
| NULL | a4 |
| NULL | NULL |
| NULL | NULL |
| NULL | NULL |
| NULL | NULL |
| NULL | NULL |

#### Optimized Results
No rows returned.
---

## Test Case 2: FULL OUTER (outer) + INNER (inner)
### Original Query
```sql
SELECT "A".col1, "A".col2 
FROM table_a "A" 
FULL OUTER JOIN (SELECT "B".col1, "B".col2 
                       FROM table_b "B" 
                       INNER JOIN table_c "C" 
                         ON "B".col2 = "C".col2) "D" 
                   ON "A".col1 = "D".col1
```
### Optimized Query
```sql
WITH D AS (
  SELECT
    "B".col1 AS col1
  FROM table_b AS "B"
  JOIN table_c AS "C"
    ON "B".col2 = "C".col2
)
SELECT
  "A".col1 AS col1,
  "A".col2 AS col2
FROM table_a AS "A"
FULL JOIN D AS D
  ON "A".col1 = "D".col1
```
**Original Row Count**: 0

**Optimized Row Count**: 0

**Match**: YES

---

## Test Case 3: FULL OUTER (outer) + LEFT (inner)
### Original Query
```sql
SELECT "A".col1, "A".col2 
FROM table_a "A" 
FULL OUTER JOIN (SELECT "B".col1, "B".col2 
                       FROM table_b "B" 
                       LEFT JOIN table_c "C" 
                         ON "B".col2 = "C".col2) "D" 
                   ON "A".col1 = "D".col1
```
### Optimized Query
```sql
WITH D AS (
  SELECT
    "B".col1 AS col1
  FROM table_b AS "B"
  LEFT JOIN table_c AS "C"
    ON "B".col2 = "C".col2
)
SELECT
  "A".col1 AS col1,
  "A".col2 AS col2
FROM table_a AS "A"
FULL JOIN D AS D
  ON "A".col1 = "D".col1
```
**Original Row Count**: 0

**Optimized Row Count**: 0

**Match**: YES

---

## Test Case 4: FULL OUTER (outer) + RIGHT (inner)
### Original Query
```sql
SELECT "A".col1, "A".col2 
FROM table_a "A" 
FULL OUTER JOIN (SELECT "B".col1, "B".col2 
                       FROM table_b "B" 
                       RIGHT JOIN table_c "C" 
                         ON "B".col2 = "C".col2) "D" 
                   ON "A".col1 = "D".col1
```
### Optimized Query
```sql
WITH D AS (
  SELECT
    "B".col1 AS col1
  FROM table_b AS "B"
  RIGHT JOIN table_c AS "C"
    ON "B".col2 = "C".col2
)
SELECT
  "A".col1 AS col1,
  "A".col2 AS col2
FROM table_a AS "A"
FULL JOIN D AS D
  ON "A".col1 = "D".col1
```
**Original Row Count**: 0

**Optimized Row Count**: 0

**Match**: YES

---

## Test Case 5: INNER (outer) + FULL OUTER (inner)
### Original Query
```sql
SELECT "A".col1, "A".col2 
FROM table_a "A" 
INNER JOIN (SELECT "B".col1, "B".col2 
                       FROM table_b "B" 
                       FULL OUTER JOIN table_c "C" 
                         ON "B".col2 = "C".col2) "D" 
                   ON "A".col1 = "D".col1
```
### Optimized Query
```sql
WITH D AS (
  SELECT
    "B".col1 AS col1
  FROM table_b AS "B"
  FULL JOIN table_c AS "C"
    ON "B".col2 = "C".col2
)
SELECT
  "A".col1 AS col1,
  "A".col2 AS col2
FROM table_a AS "A"
JOIN D AS D
  ON "A".col1 = "D".col1
```
**Original Row Count**: 0

**Optimized Row Count**: 0

**Match**: YES

---

## Test Case 6: INNER (outer) + INNER (inner)
### Original Query
```sql
SELECT "A".col1, "A".col2 
FROM table_a "A" 
INNER JOIN (SELECT "B".col1, "B".col2 
                       FROM table_b "B" 
                       INNER JOIN table_c "C" 
                         ON "B".col2 = "C".col2) "D" 
                   ON "A".col1 = "D".col1
```
### Optimized Query
```sql
WITH D AS (
  SELECT
    "B".col1 AS col1
  FROM table_b AS "B"
  JOIN table_c AS "C"
    ON "B".col2 = "C".col2
)
SELECT
  "A".col1 AS col1,
  "A".col2 AS col2
FROM table_a AS "A"
JOIN D AS D
  ON "A".col1 = "D".col1
```
**Original Row Count**: 0

**Optimized Row Count**: 0

**Match**: YES

---

## Test Case 7: INNER (outer) + LEFT (inner)
### Original Query
```sql
SELECT "A".col1, "A".col2 
FROM table_a "A" 
INNER JOIN (SELECT "B".col1, "B".col2 
                       FROM table_b "B" 
                       LEFT JOIN table_c "C" 
                         ON "B".col2 = "C".col2) "D" 
                   ON "A".col1 = "D".col1
```
### Optimized Query
```sql
WITH D AS (
  SELECT
    "B".col1 AS col1
  FROM table_b AS "B"
  LEFT JOIN table_c AS "C"
    ON "B".col2 = "C".col2
)
SELECT
  "A".col1 AS col1,
  "A".col2 AS col2
FROM table_a AS "A"
JOIN D AS D
  ON "A".col1 = "D".col1
```
**Original Row Count**: 0

**Optimized Row Count**: 0

**Match**: YES

---

## Test Case 8: INNER (outer) + RIGHT (inner)
### Original Query
```sql
SELECT "A".col1, "A".col2 
FROM table_a "A" 
INNER JOIN (SELECT "B".col1, "B".col2 
                       FROM table_b "B" 
                       RIGHT JOIN table_c "C" 
                         ON "B".col2 = "C".col2) "D" 
                   ON "A".col1 = "D".col1
```
### Optimized Query
```sql
WITH D AS (
  SELECT
    "B".col1 AS col1
  FROM table_b AS "B"
  RIGHT JOIN table_c AS "C"
    ON "B".col2 = "C".col2
)
SELECT
  "A".col1 AS col1,
  "A".col2 AS col2
FROM table_a AS "A"
JOIN D AS D
  ON "A".col1 = "D".col1
```
**Original Row Count**: 0

**Optimized Row Count**: 0

**Match**: YES

---

## Test Case 9: LEFT (outer) + FULL OUTER (inner)
### Original Query
```sql
SELECT "A".col1, "A".col2 
FROM table_a "A" 
LEFT JOIN (SELECT "B".col1, "B".col2 
                       FROM table_b "B" 
                       FULL OUTER JOIN table_c "C" 
                         ON "B".col2 = "C".col2) "D" 
                   ON "A".col1 = "D".col1
```
### Optimized Query
```sql
WITH D AS (
  SELECT
    "B".col1 AS col1
  FROM table_b AS "B"
  FULL JOIN table_c AS "C"
    ON "B".col2 = "C".col2
)
SELECT
  "A".col1 AS col1,
  "A".col2 AS col2
FROM table_a AS "A"
LEFT JOIN D AS D
  ON "A".col1 = "D".col1
```
**Original Row Count**: 0

**Optimized Row Count**: 0

**Match**: YES

---

## Test Case 10: LEFT (outer) + INNER (inner)
### Original Query
```sql
SELECT "A".col1, "A".col2 
FROM table_a "A" 
LEFT JOIN (SELECT "B".col1, "B".col2 
                       FROM table_b "B" 
                       INNER JOIN table_c "C" 
                         ON "B".col2 = "C".col2) "D" 
                   ON "A".col1 = "D".col1
```
### Optimized Query
```sql
WITH D AS (
  SELECT
    "B".col1 AS col1
  FROM table_b AS "B"
  JOIN table_c AS "C"
    ON "B".col2 = "C".col2
)
SELECT
  "A".col1 AS col1,
  "A".col2 AS col2
FROM table_a AS "A"
LEFT JOIN D AS D
  ON "A".col1 = "D".col1
```
**Original Row Count**: 0

**Optimized Row Count**: 0

**Match**: YES

---

## Test Case 11: LEFT (outer) + LEFT (inner)
### Original Query
```sql
SELECT "A".col1, "A".col2 
FROM table_a "A" 
LEFT JOIN (SELECT "B".col1, "B".col2 
                       FROM table_b "B" 
                       LEFT JOIN table_c "C" 
                         ON "B".col2 = "C".col2) "D" 
                   ON "A".col1 = "D".col1
```
### Optimized Query
```sql
WITH D AS (
  SELECT
    "B".col1 AS col1
  FROM table_b AS "B"
  LEFT JOIN table_c AS "C"
    ON "B".col2 = "C".col2
)
SELECT
  "A".col1 AS col1,
  "A".col2 AS col2
FROM table_a AS "A"
LEFT JOIN D AS D
  ON "A".col1 = "D".col1
```
**Original Row Count**: 0

**Optimized Row Count**: 0

**Match**: YES

---

## Test Case 12: LEFT (outer) + RIGHT (inner)
### Original Query
```sql
SELECT "A".col1, "A".col2 
FROM table_a "A" 
LEFT JOIN (SELECT "B".col1, "B".col2 
                       FROM table_b "B" 
                       RIGHT JOIN table_c "C" 
                         ON "B".col2 = "C".col2) "D" 
                   ON "A".col1 = "D".col1
```
### Optimized Query
```sql
WITH D AS (
  SELECT
    "B".col1 AS col1
  FROM table_b AS "B"
  RIGHT JOIN table_c AS "C"
    ON "B".col2 = "C".col2
)
SELECT
  "A".col1 AS col1,
  "A".col2 AS col2
FROM table_a AS "A"
LEFT JOIN D AS D
  ON "A".col1 = "D".col1
```
**Original Row Count**: 0

**Optimized Row Count**: 0

**Match**: YES

---

## Test Case 13: RIGHT (outer) + FULL OUTER (inner)
### Original Query
```sql
SELECT "A".col1, "A".col2 
FROM table_a "A" 
RIGHT JOIN (SELECT "B".col1, "B".col2 
                       FROM table_b "B" 
                       FULL OUTER JOIN table_c "C" 
                         ON "B".col2 = "C".col2) "D" 
                   ON "A".col1 = "D".col1
```
### Optimized Query
```sql
WITH D AS (
  SELECT
    "B".col1 AS col1
  FROM table_b AS "B"
  FULL JOIN table_c AS "C"
    ON "B".col2 = "C".col2
)
SELECT
  "A".col1 AS col1,
  "A".col2 AS col2
FROM table_a AS "A"
RIGHT JOIN D AS D
  ON "A".col1 = "D".col1
```
**Original Row Count**: 0

**Optimized Row Count**: 0

**Match**: YES

---

## Test Case 14: RIGHT (outer) + INNER (inner)
### Original Query
```sql
SELECT "A".col1, "A".col2 
FROM table_a "A" 
RIGHT JOIN (SELECT "B".col1, "B".col2 
                       FROM table_b "B" 
                       INNER JOIN table_c "C" 
                         ON "B".col2 = "C".col2) "D" 
                   ON "A".col1 = "D".col1
```
### Optimized Query
```sql
WITH D AS (
  SELECT
    "B".col1 AS col1
  FROM table_b AS "B"
  JOIN table_c AS "C"
    ON "B".col2 = "C".col2
)
SELECT
  "A".col1 AS col1,
  "A".col2 AS col2
FROM table_a AS "A"
RIGHT JOIN D AS D
  ON "A".col1 = "D".col1
```
**Original Row Count**: 0

**Optimized Row Count**: 0

**Match**: YES

---

## Test Case 15: RIGHT (outer) + LEFT (inner)
### Original Query
```sql
SELECT "A".col1, "A".col2 
FROM table_a "A" 
RIGHT JOIN (SELECT "B".col1, "B".col2 
                       FROM table_b "B" 
                       LEFT JOIN table_c "C" 
                         ON "B".col2 = "C".col2) "D" 
                   ON "A".col1 = "D".col1
```
### Optimized Query
```sql
WITH D AS (
  SELECT
    "B".col1 AS col1
  FROM table_b AS "B"
  LEFT JOIN table_c AS "C"
    ON "B".col2 = "C".col2
)
SELECT
  "A".col1 AS col1,
  "A".col2 AS col2
FROM table_a AS "A"
RIGHT JOIN D AS D
  ON "A".col1 = "D".col1
```
**Original Row Count**: 0

**Optimized Row Count**: 0

**Match**: YES

---

## Test Case 16: RIGHT (outer) + RIGHT (inner)
### Original Query
```sql
SELECT "A".col1, "A".col2 
FROM table_a "A" 
RIGHT JOIN (SELECT "B".col1, "B".col2 
                       FROM table_b "B" 
                       RIGHT JOIN table_c "C" 
                         ON "B".col2 = "C".col2) "D" 
                   ON "A".col1 = "D".col1
```
### Optimized Query
```sql
WITH D AS (
  SELECT
    "B".col1 AS col1
  FROM table_b AS "B"
  RIGHT JOIN table_c AS "C"
    ON "B".col2 = "C".col2
)
SELECT
  "A".col1 AS col1,
  "A".col2 AS col2
FROM table_a AS "A"
RIGHT JOIN D AS D
  ON "A".col1 = "D".col1
```
**Original Row Count**: 0

**Optimized Row Count**: 0

**Match**: YES

---

## Summary Table

| Outer Join | Inner Join | Original Rows | Optimized Rows | Match |
|------------|------------|---------------|----------------|-------|
| FULL OUTER | FULL OUTER | 9 | 0 | <span style="color:red">NO - POTENTIAL BUG</span> |
| FULL OUTER | INNER | 0 | 0 | YES |
| FULL OUTER | LEFT | 0 | 0 | YES |
| FULL OUTER | RIGHT | 0 | 0 | YES |
| INNER | FULL OUTER | 0 | 0 | YES |
| INNER | INNER | 0 | 0 | YES |
| INNER | LEFT | 0 | 0 | YES |
| INNER | RIGHT | 0 | 0 | YES |
| LEFT | FULL OUTER | 0 | 0 | YES |
| LEFT | INNER | 0 | 0 | YES |
| LEFT | LEFT | 0 | 0 | YES |
| LEFT | RIGHT | 0 | 0 | YES |
| RIGHT | FULL OUTER | 0 | 0 | YES |
| RIGHT | INNER | 0 | 0 | YES |
| RIGHT | LEFT | 0 | 0 | YES |
| RIGHT | RIGHT | 0 | 0 | YES |
