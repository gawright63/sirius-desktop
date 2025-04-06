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
SELECT
  "A"."col1" AS "col1",
  "A"."col2" AS "col2"
FROM "table_a" AS "A"
FULL JOIN "table_b" AS "B"
  ON "A"."col1" = "B"."col1"
FULL JOIN "table_c" AS "C"
  ON "B"."col2" = "C"."col2"
```
**Original Row Count**: 9

**Optimized Row Count**: 9

**Match**: YES

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
SELECT
  "A"."col1" AS "col1",
  "A"."col2" AS "col2"
FROM "table_a" AS "A"
FULL JOIN "table_b" AS "B"
  ON "A"."col1" = "B"."col1"
JOIN "table_c" AS "C"
  ON "B"."col2" = "C"."col2"
```
**Original Row Count**: 7

**Optimized Row Count**: 3

**Match**: NO - POTENTIAL BUG

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
SELECT
  "A"."col1" AS "col1",
  "A"."col2" AS "col2"
FROM "table_a" AS "A"
FULL JOIN "table_b" AS "B"
  ON "A"."col1" = "B"."col1"
LEFT JOIN "table_c" AS "C"
  ON "B"."col2" = "C"."col2"
```
**Original Row Count**: 8

**Optimized Row Count**: 8

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
SELECT
  "A"."col1" AS "col1",
  "A"."col2" AS "col2"
FROM "table_a" AS "A"
FULL JOIN "table_b" AS "B"
  ON "A"."col1" = "B"."col1"
RIGHT JOIN "table_c" AS "C"
  ON "B"."col2" = "C"."col2"
```
**Original Row Count**: 8

**Optimized Row Count**: 4

**Match**: NO - POTENTIAL BUG

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
SELECT
  "A"."col1" AS "col1",
  "A"."col2" AS "col2"
FROM "table_a" AS "A"
JOIN "table_b" AS "B"
  ON "A"."col1" = "B"."col1"
FULL JOIN "table_c" AS "C"
  ON "B"."col2" = "C"."col2"
```
**Original Row Count**: 0

**Optimized Row Count**: 4

**Match**: NO - POTENTIAL BUG

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
SELECT
  "A"."col1" AS "col1",
  "A"."col2" AS "col2"
FROM "table_a" AS "A"
JOIN "table_b" AS "B"
  ON "A"."col1" = "B"."col1"
JOIN "table_c" AS "C"
  ON "B"."col2" = "C"."col2"
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
SELECT
  "A"."col1" AS "col1",
  "A"."col2" AS "col2"
FROM "table_a" AS "A"
JOIN "table_b" AS "B"
  ON "A"."col1" = "B"."col1"
LEFT JOIN "table_c" AS "C"
  ON "B"."col2" = "C"."col2"
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
SELECT
  "A"."col1" AS "col1",
  "A"."col2" AS "col2"
FROM "table_a" AS "A"
JOIN "table_b" AS "B"
  ON "A"."col1" = "B"."col1"
RIGHT JOIN "table_c" AS "C"
  ON "B"."col2" = "C"."col2"
```
**Original Row Count**: 0

**Optimized Row Count**: 4

**Match**: NO - POTENTIAL BUG

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
SELECT
  "A"."col1" AS "col1",
  "A"."col2" AS "col2"
FROM "table_a" AS "A"
LEFT JOIN "table_b" AS "B"
  ON "A"."col1" = "B"."col1"
FULL JOIN "table_c" AS "C"
  ON "B"."col2" = "C"."col2"
```
**Original Row Count**: 4

**Optimized Row Count**: 8

**Match**: NO - POTENTIAL BUG

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
SELECT
  "A"."col1" AS "col1",
  "A"."col2" AS "col2"
FROM "table_a" AS "A"
LEFT JOIN "table_b" AS "B"
  ON "A"."col1" = "B"."col1"
JOIN "table_c" AS "C"
  ON "B"."col2" = "C"."col2"
```
**Original Row Count**: 4

**Optimized Row Count**: 0

**Match**: NO - POTENTIAL BUG

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
SELECT
  "A"."col1" AS "col1",
  "A"."col2" AS "col2"
FROM "table_a" AS "A"
LEFT JOIN "table_b" AS "B"
  ON "A"."col1" = "B"."col1"
LEFT JOIN "table_c" AS "C"
  ON "B"."col2" = "C"."col2"
```
**Original Row Count**: 4

**Optimized Row Count**: 4

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
SELECT
  "A"."col1" AS "col1",
  "A"."col2" AS "col2"
FROM "table_a" AS "A"
LEFT JOIN "table_b" AS "B"
  ON "A"."col1" = "B"."col1"
RIGHT JOIN "table_c" AS "C"
  ON "B"."col2" = "C"."col2"
```
**Original Row Count**: 4

**Optimized Row Count**: 4

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
SELECT
  "A"."col1" AS "col1",
  "A"."col2" AS "col2"
FROM "table_a" AS "A"
RIGHT JOIN "table_b" AS "B"
  ON "A"."col1" = "B"."col1"
FULL JOIN "table_c" AS "C"
  ON "B"."col2" = "C"."col2"
```
**Original Row Count**: 5

**Optimized Row Count**: 5

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
SELECT
  "A"."col1" AS "col1",
  "A"."col2" AS "col2"
FROM "table_a" AS "A"
RIGHT JOIN "table_b" AS "B"
  ON "A"."col1" = "B"."col1"
JOIN "table_c" AS "C"
  ON "B"."col2" = "C"."col2"
```
**Original Row Count**: 3

**Optimized Row Count**: 3

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
SELECT
  "A"."col1" AS "col1",
  "A"."col2" AS "col2"
FROM "table_a" AS "A"
RIGHT JOIN "table_b" AS "B"
  ON "A"."col1" = "B"."col1"
LEFT JOIN "table_c" AS "C"
  ON "B"."col2" = "C"."col2"
```
**Original Row Count**: 4

**Optimized Row Count**: 4

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
SELECT
  "A"."col1" AS "col1",
  "A"."col2" AS "col2"
FROM "table_a" AS "A"
RIGHT JOIN "table_b" AS "B"
  ON "A"."col1" = "B"."col1"
RIGHT JOIN "table_c" AS "C"
  ON "B"."col2" = "C"."col2"
```
**Original Row Count**: 4

**Optimized Row Count**: 4

**Match**: YES

---

## Summary Table

| Outer Join | Inner Join | Original Rows | Optimized Rows | Match |
|------------|------------|---------------|----------------|-------|
| FULL OUTER | FULL OUTER | 9 | 9 | YES |
| FULL OUTER | INNER | 7 | 3 | <span style="color:red">NO - POTENTIAL BUG</span> |
| FULL OUTER | LEFT | 8 | 8 | YES |
| FULL OUTER | RIGHT | 8 | 4 | <span style="color:red">NO - POTENTIAL BUG</span> |
| INNER | FULL OUTER | 0 | 4 | <span style="color:red">NO - POTENTIAL BUG</span> |
| INNER | INNER | 0 | 0 | YES |
| INNER | LEFT | 0 | 0 | YES |
| INNER | RIGHT | 0 | 4 | <span style="color:red">NO - POTENTIAL BUG</span> |
| LEFT | FULL OUTER | 4 | 8 | <span style="color:red">NO - POTENTIAL BUG</span> |
| LEFT | INNER | 4 | 0 | <span style="color:red">NO - POTENTIAL BUG</span> |
| LEFT | LEFT | 4 | 4 | YES |
| LEFT | RIGHT | 4 | 4 | YES |
| RIGHT | FULL OUTER | 5 | 5 | YES |
| RIGHT | INNER | 3 | 3 | YES |
| RIGHT | LEFT | 4 | 4 | YES |
| RIGHT | RIGHT | 4 | 4 | YES |
