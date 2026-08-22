## SET OPERATION BETWEEN TABLES
- Get common data between tables (INTERSECT)
- Get all data across tables (UNION)
- Get data present in one table but not in the other (EXCEPT)
```sql
/*******************************************************************************
1. SETUP: CLEAN UP AND RECREATE TABLES
*******************************************************************************/
DROP TABLE IF EXISTS EMPLOYEES_A;
DROP TABLE IF EXISTS EMPLOYEES_B;
GO

CREATE TABLE EMPLOYEES_A (
    EMP_ID INT,
    EMP_NAME VARCHAR(50)
);

CREATE TABLE EMPLOYEES_B (
    EMP_ID INT,
    EMP_NAME VARCHAR(50)
);
GO
/*******************************************************************************
2. DATA ENTRY: INSERT SAMPLE DATA
*******************************************************************************/
-- Employees in Department A
INSERT INTO EMPLOYEES_A VALUES
(1, 'Alice'),
(2, 'Bob'),
(3, 'Charlie'),
(4, 'David');

-- Employees in Department B
INSERT INTO EMPLOYEES_B VALUES
(3, 'Charlie'),
(4, 'David'),
(5, 'Eva'),
(6, 'Frank');
GO
/*******************************************************************************
3. DISPLAY INPUT DATA
*******************************************************************************/
SELECT * FROM EMPLOYEES_A;
SELECT * FROM EMPLOYEES_B;
GO
/*******************************************************************************
4. SET OPERATIONS
*******************************************************************************/

-- Example 1: UNION → All unique employees across both departments
SELECT EMP_NAME FROM EMPLOYEES_A
UNION
SELECT EMP_NAME FROM EMPLOYEES_B;

-- Example 2: INTERSECT → Employees common to both departments
SELECT EMP_NAME FROM EMPLOYEES_A
INTERSECT
SELECT EMP_NAME FROM EMPLOYEES_B;

-- Example 3: EXCEPT → Employees in Department A but not in Department B
SELECT EMP_NAME FROM EMPLOYEES_A
EXCEPT
SELECT EMP_NAME FROM EMPLOYEES_B;

-- Example 4: EXCEPT (reverse) → Employees in Department B but not in Department A
SELECT EMP_NAME FROM EMPLOYEES_B
EXCEPT
SELECT EMP_NAME FROM EMPLOYEES_A;
GO
```
