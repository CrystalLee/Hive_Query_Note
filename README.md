# Hadoop Hive Analytic Functions and Examples

Referenced from
- https://dwgeek.com/hadoop-hive-analytic-functions-examples.html/
- https://popsql.com/learn-sql/postgresql/how-to-calculate-cumulative-sum-running-total-in-postgresql/


## Analytic Functions
1. COUNT
1. SUM and cumulative SUM
1. MIN and MAX
1. LEAD and LAG
1. FIRST_VALUE and LAST_VALUE
1. ROW_NUMBER, RANK and DENSE_RANK


### 1. COUNT()

##### Syntax:

`COUNT(column reference | value expression | *) over(window_spec)`


##### Example:

```
SELECT
  *,
  COUNT(*) over(PARTITION BY Date ORDER BY Date ASC) cnt
FROM
    `ClockRecord`
  ;
```

| ID  | Date       | Name | cnt |
| --- | ---------- | ---- | --- |
| 1   | 2020-01-01 | Alex | 4   |
| 2   | 2020-01-01 | Sam  | 4   |
| 3   | 2020-01-01 | Rose | 4   |
| 4   | 2020-01-01 | John | 4   |
| 5   | 2020-01-02 | Alex | 3   |
| 6   | 2020-01-02 | Sam  | 3   |
| 7   | 2020-01-02 | Rose | 3   |
| 8   | 2020-01-03 | John | 1   |
| 9   | 2020-01-04 | John | 1   |

Query Example https://www.db-fiddle.com/f/xti4UmPATu1dzCkA4s98C9/0

----

### 2. SUM

##### Syntax:

`SUM(column | expression) OVER( window_spec)`

##### Example:
```
SELECT
  *,
  sum(Salary) over (partition by Date order by Date asc) total_salary,
  sum(Salary) over (PARTITION BY Date ORDER BY Date ASC rows between unbounded preceding and current row) cumulative

FROM
    `ClockRecord`
  ;

```

| ID  | Date       | Name | Salary | total_salary | cumulative_salary |
| --- | ---------- | ---- | ------ | ------------ | ---------- |
| 1   | 2020-01-01 | Alex | 100    | 1100         | 100        |
| 2   | 2020-01-01 | Sam  | 500    | 1100         | 600        |
| 3   | 2020-01-01 | Rose | 300    | 1100         | 900        |
| 4   | 2020-01-01 | John | 200    | 1100         | 1100       |
| 5   | 2020-01-02 | Alex | 400    | 1300         | 400        |
| 6   | 2020-01-02 | Sam  | 600    | 1300         | 1000       |
| 7   | 2020-01-02 | Rose | 300    | 1300         | 1300       |
| 8   | 2020-01-03 | John | 100    | 100          | 100        |
| 9   | 2020-01-04 | John | 200    | 200          | 200        |

Query Example https://www.db-fiddle.com/f/xti4UmPATu1dzCkA4s98C9/1

----

### 3. MIN() and MAX()

##### Syntax:
```
MIN(column | expression) OVER( window_spec)
MAX(column | expression) OVER( window_spec)
```

##### Example:

```
SELECT
  *,
  min(Salary) over (partition by Date order by Date asc) min_Salary,
  max(Salary) over (partition by Date order by Date asc) max_Salary  
FROM
  `ClockRecord`
;
```

| ID  | Date       | Name | Salary | min_Salary | max_Salary |
| --- | ---------- | ---- | ------ | ---------- | ---------- |
| 1   | 2020-01-01 | Alex | 100    | 100        | 500        |
| 2   | 2020-01-01 | Sam  | 500    | 100        | 500        |
| 3   | 2020-01-01 | Rose | 300    | 100        | 500        |
| 4   | 2020-01-01 | John | 200    | 100        | 500        |
| 5   | 2020-01-02 | Alex | 400    | 300        | 600        |
| 6   | 2020-01-02 | Sam  | 600    | 300        | 600        |
| 7   | 2020-01-02 | Rose | 300    | 300        | 600        |
| 8   | 2020-01-03 | John | 100    | 100        | 100        |
| 9   | 2020-01-04 | John | 200    | 200        | 200        |


Query Example https://www.db-fiddle.com/f/xti4UmPATu1dzCkA4s98C9/2

---
### 4. LEAD() and LAG()

##### Syntax:

```
LEAD(column, offset, default) OVER( window_spec)
LAG(column, offset, default) OVER( window_spec)
```

##### Example:

```
SELECT
  *,
  Lead(Salary) over (partition by Date order by Date asc) Lead_Salary,
  Lag(Salary) over (partition by Date order by Date asc)  Lag_Salary  
FROM
  `ClockRecord`
  ;
```
| ID  | Date       | Name | Salary | Lead_Salary | Lag_Salary |
| --- | ---------- | ---- | ------ | ---------- | ---------- |
| 1   | 2020-01-01 | Alex | 100    | 500        | NULL         |
| 2   | 2020-01-01 | Sam  | 500    | 300        | 100        |
| 3   | 2020-01-01 | Rose | 300    | 200        | 500        |
| 4   | 2020-01-01 | John | 200    | NULL         | 300        |
| 5   | 2020-01-02 | Alex | 400    | 600        |    NULL        |
| 6   | 2020-01-02 | Sam  | 600    | 300        | 400        |
| 7   | 2020-01-02 | Rose | 300    | NULL        | 600        |
| 8   | 2020-01-03 | John | 100    | NULL           |    NULL        |
| 9   | 2020-01-04 | John | 200    | NULL           |    NULL        |

Query Example https://www.db-fiddle.com/f/xti4UmPATu1dzCkA4s98C9/3

---

### 5. FIRST_VALUE() and LAST_VALUE()

##### Syntax:

```
FIRST_VALUE(column | expression) OVER( window_spec)
LAST_VALUE(column | expression) OVER( window_spec RANGE BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING)
```

##### Example:

```
SELECT
  *,
  FIRST_VALUE(Salary) over (partition by Date order by Salary ASC) First_Salary,
  LAST_VALUE(Salary) over (partition by Date order by Salary ASC) Last_Salary_without_range,
  LAST_VALUE(Salary) over (partition by Date order by Salary ASC RANGE BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING)  Last_Salary  
FROM
  `ClockRecord`
  ;

```
| ID  | Date       | Name | Salary | First_Salary | Last_Salary_without_range | Last_Salary |
| --- | ---------- | ---- | ------ | ------------ | ------------------------- | ----------- |
| 1   | 2020-01-01 | Alex | 100    | 100          | 100                       | 500         |
| 4   | 2020-01-01 | John | 200    | 100          | 200                       | 500         |
| 3   | 2020-01-01 | Rose | 300    | 100          | 300                       | 500         |
| 2   | 2020-01-01 | Sam  | 500    | 100          | 500                       | 500         |
| 7   | 2020-01-02 | Rose | 300    | 300          | 300                       | 600         |
| 5   | 2020-01-02 | Alex | 400    | 300          | 400                       | 600         |
| 6   | 2020-01-02 | Sam  | 600    | 300          | 600                       | 600         |
| 8   | 2020-01-03 | John | 100    | 100          | 100                       | 100         |
| 9   | 2020-01-04 | John | 200    | 200          | 200                       | 200         |


Query Example https://www.db-fiddle.com/f/xti4UmPATu1dzCkA4s98C9/4

---

### 6. ROW_NUMBER() and DENSE_RANK()

##### Syntax:

```
ROW_NUMBER() OVER( window_spec)
DENSE_RANK() OVER( window_spec)
```

##### Example:

```
SELECT
  *,
  ROW_NUMBER() over (partition by Date order by Date ASC) as rn,
  DENSE_RANK() over (partition by Date order by Date ASC) as dn
FROM
  `ClockRecord`
;
```
| ID  | Date       | Name | Salary | rn  | dn  |
| --- | ---------- | ---- | ------ | --- | --- |
| 1   | 2020-01-01 | Alex | 100    | 1   | 1   |
| 2   | 2020-01-01 | Sam  | 500    | 2   | 1   |
| 3   | 2020-01-01 | Rose | 300    | 3   | 1   |
| 4   | 2020-01-01 | John | 200    | 4   | 1   |
| 5   | 2020-01-02 | Alex | 400    | 1   | 1   |
| 6   | 2020-01-02 | Sam  | 600    | 2   | 1   |
| 7   | 2020-01-02 | Rose | 300    | 3   | 1   |
| 8   | 2020-01-03 | John | 100    | 1   | 1   |
| 9   | 2020-01-04 | John | 200    | 1   | 1   |

Query Example https://www.db-fiddle.com/f/xti4UmPATu1dzCkA4s98C9/6
