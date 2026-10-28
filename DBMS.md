# Database Management System (DBMS)

A complete guide covering core DBMS concepts with detailed explanations, followed by scenario-based questions and answers.

---

# Introduction to DBMS

## What is a DBMS?

A Database Management System (DBMS) is software that enables users to define, create, maintain, and control access to a database. It acts as an intermediary between the user/application and the physical database, handling data storage, retrieval, security, and integrity.

## Why Use a DBMS?

- Reduces data redundancy through centralized storage
- Enforces data consistency and integrity via constraints
- Provides controlled concurrent access to multiple users
- Offers backup and recovery mechanisms
- Provides security through authentication and authorization
- Supports efficient querying using languages like SQL

## DBMS vs File System

| Aspect | File System | DBMS |
|---|---|---|
| Redundancy | High | Minimized |
| Data Integrity | Hard to enforce | Enforced via constraints |
| Concurrent Access | Difficult, unsafe | Managed safely |
| Security | Limited | Strong, role-based |
| Backup/Recovery | Manual | Built-in mechanisms |
| Query Capability | Program-dependent | Declarative (SQL) |

## Three-Schema Architecture (ANSI-SPARC)

- **Internal Level**: Describes physical storage structure of data.
- **Conceptual Level**: Describes the structure of the whole database for a community of users (logical schema).
- **External Level**: Describes the part of the database relevant to a particular user (views).

## Data Independence

- **Logical Data Independence**: Ability to change the conceptual schema without changing external schemas/applications.
- **Physical Data Independence**: Ability to change the internal (storage) schema without changing the conceptual schema.

## Database Users

- **DBA (Database Administrator)**: Manages schema, security, backup/recovery, performance.
- **Naive Users**: Interact via predefined applications (e.g., ATM users).
- **Application Programmers**: Write programs that interact with the database.
- **Sophisticated Users**: Write and execute queries directly (analysts).

---

# Data Models

## Hierarchical Model

Organizes data in a tree-like structure where each child record has only one parent. Fast for one-to-many relationships but rigid and difficult to restructure.

## Network Model

Similar to the hierarchical model but allows a record to have multiple parent and child records, forming a graph-like structure. More flexible but complex to design and query.

## Relational Model

Data is organized into tables (relations) consisting of rows (tuples) and columns (attributes). This is the most widely used model today because of its simplicity, mathematical foundation (relational algebra), and support for declarative querying via SQL.

## Object-Oriented Model

Combines database capabilities with object-oriented programming concepts, storing data as objects, similar to classes and instances in OOP languages.

## Entity-Relationship (ER) Model

A conceptual model used during database design that represents data as entities, attributes, and relationships before it is translated into a relational schema.

---

# Entity-Relationship (ER) Diagram

## Core Components

### Entity
A real-world object or concept that has independent existence, such as `Student` or `Course`. Represented as a rectangle.

### Attribute
A property or characteristic of an entity, such as `StudentName` or `RollNumber`. Represented as an oval.

- **Simple Attribute**: Cannot be divided further (e.g., Age)
- **Composite Attribute**: Can be divided into sub-parts (e.g., Address → Street, City, Zip)
- **Derived Attribute**: Can be derived from other attributes (e.g., Age from DateOfBirth)
- **Multivalued Attribute**: Can hold multiple values (e.g., PhoneNumbers)

### Relationship
An association between two or more entities, such as `Enrolls` between `Student` and `Course`. Represented as a diamond.

## Cardinality

Defines the numerical relationship between entities:

- **One-to-One (1:1)**: One entity instance relates to exactly one instance of another entity
- **One-to-Many (1:N)**: One entity instance relates to many instances of another entity
- **Many-to-Many (M:N)**: Multiple instances of one entity relate to multiple instances of another

## Strong vs Weak Entity

A **strong entity** has a primary key of its own. A **weak entity** depends on a strong (owner) entity for identification and uses a partial key combined with the owner's key.

## Participation Constraints

- **Total Participation**: Every entity instance must participate in the relationship (double line in ER diagram).
- **Partial Participation**: Entity participation is optional (single line).

## Generalization, Specialization, and Aggregation

- **Generalization**: Bottom-up process combining lower-level entities into a higher-level entity (e.g., Car + Truck → Vehicle).
- **Specialization**: Top-down process dividing a higher-level entity into sub-entities (e.g., Employee → Manager, Engineer).
- **Aggregation**: Treats a relationship as a higher-level entity, allowing it to participate in other relationships.

---

# Relational Model and Keys

## Relation Basics

A relation is a table with rows (tuples) and columns (attributes). Each relation has a defined schema specifying attribute names and data types.

## Types of Keys

### Super Key
A set of one or more attributes that can uniquely identify a tuple in a relation.

### Candidate Key
A minimal super key — no attribute can be removed without losing the uniqueness property. A relation can have multiple candidate keys.

### Primary Key
A candidate key chosen by the database designer to uniquely identify tuples. It cannot contain NULL values.

### Alternate Key
Candidate keys that are not selected as the primary key.

### Foreign Key
An attribute in one relation that references the primary key of another relation, used to enforce referential integrity between tables.

### Composite Key
A key made up of two or more attributes that together uniquely identify a tuple.

### Super Key vs Candidate Key vs Primary Key

Every primary key is a candidate key, every candidate key is a super key, but the reverse is not always true.

---

# Relational Algebra

Procedural query language operating on relations, forming the theoretical basis for SQL.

- **Selection (σ)**: Selects rows satisfying a condition.
- **Projection (π)**: Selects specific columns.
- **Union (∪)**: Combines tuples from two union-compatible relations.
- **Set Difference (−)**: Tuples in one relation but not another.
- **Cartesian Product (×)**: Combines every tuple of one relation with every tuple of another.
- **Rename (ρ)**: Renames a relation or its attributes.
- **Join (⋈)**: Derived operation combining selection and Cartesian product based on a condition.

---

# Normalization

## Purpose

Normalization is the process of organizing data to minimize redundancy and avoid update, insertion, and deletion anomalies by decomposing tables based on functional dependencies.

## Functional Dependency

A constraint where one attribute uniquely determines another. Written as `A → B`, meaning the value of A determines the value of B.

## Normal Forms

### First Normal Form (1NF)
A table is in 1NF if all attributes contain only atomic (indivisible) values and each record is unique, with no repeating groups or arrays.

Rule: Each column must contain a single (atomic) value. There should be no multivalued cells or repeating groups.

Suppose we have this table:

Before 1NF

| Student_ID | Name  | Subjects     |
| ---------- | ----- | ------------ |
| 101        | Rahul | DBMS, SQL    |
| 102        | Priya | Java, Python |

The `Subjects` column contains multiple values in one cell, so it violates 1NF.

After 1NF

| Student_ID | Name  | Subject |
| ---------- | ----- | ------- |
| 101        | Rahul | DBMS    |
| 101        | Rahul | SQL     |
| 102        | Priya | Java    |
| 102        | Priya | Python  |

SQL example:

```
CREATE TABLE Student_Subject (
    Student_ID INT,
    Name VARCHAR(50),
    Subject VARCHAR(50),
    PRIMARY KEY (Student_ID, Subject)
);
```

Remember: One cell = one value.

### Second Normal Form (2NF)
A table is in 2NF if it is in 1NF and every non-key attribute is fully functionally dependent on the entire primary key (removes partial dependency, relevant for composite keys).

Rules:

- The table must already be in 1NF.
- Every non-key attribute must depend on the whole candidate key, not just part of a composite key.

Consider this table:

Before 2NF

| Student_ID | Course_ID | Student_Name | Course_Name |
| ---------- | --------- | ------------ | ----------- |
| 101        | C1        | Rahul        | DBMS        |
| 101        | C2        | Rahul        | Java        |
| 102        | C1        | Priya        | DBMS        |

Composite primary key: (Student_ID, Course_ID)

Here:

- `Student_ID → Student_Name`
- `Course_ID → Course_Name`
- The complete key `(Student_ID, Course_ID)` identifies a student's course enrollment.

But `Student_Name` depends only on `Student_ID`, and `Course_Name` depends only on `Course_ID`. These are partial dependencies, which violate 2NF.

After 2NF

Student table

| Student_ID (PK) | Student_Name |
| --------------- | ------------ |
| 101             | Rahul        |
| 102             | Priya        |

Course table

| Course_ID (PK) | Course_Name |
| -------------- | ----------- |
| C1             | DBMS        |
| C2             | Java        |

Enrollment table

| Student_ID (PK, FK) | Course_ID (PK, FK) |
| ------------------- | ------------------ |
| 101                 | C1                 |
| 101                 | C2                 |
| 102                 | C1                 |

SQL example:

```
CREATE TABLE Student (
    Student_ID INT PRIMARY KEY,
    Student_Name VARCHAR(50)
);

CREATE TABLE Course (
    Course_ID VARCHAR(10) PRIMARY KEY,
    Course_Name VARCHAR(50)
);

CREATE TABLE Enrollment (
    Student_ID INT,
    Course_ID VARCHAR(10),
    PRIMARY KEY (Student_ID, Course_ID),
    FOREIGN KEY (Student_ID) REFERENCES Student(Student_ID),
    FOREIGN KEY (Course_ID) REFERENCES Course(Course_ID)
);
```

Remember: No partial dependency on a composite key.

### Third Normal Form (3NF)
A table is in 3NF if it is in 2NF and has no transitive dependency — non-key attributes must depend only on the primary key, not on other non-key attributes.

Rules:

- The table must already be in 2NF.
- There must be no transitive dependency of non-key attributes on a candidate key.

Consider this table:

Before 3NF

| Student_ID (PK) | Student_Name | Dept_ID | Dept_Name        |
| --------------- | ------------ | ------- | ---------------- |
| 101             | Rahul        | D1      | Computer Science |
| 102             | Priya        | D2      | Mathematics      |
| 103             | Amit         | D1      | Computer Science |

Dependencies:

- `Student_ID → Dept_ID`
- `Dept_ID → Dept_Name`

Therefore, `Student_ID → Dept_Name` indirectly through `Dept_ID`.

`Dept_Name` depends on `Dept_ID`, not directly on the student key. This is a transitive dependency.

After 3NF

Student table

| Student_ID (PK) | Student_Name | Dept_ID (FK) |
| --------------- | ------------ | ------------ |
| 101             | Rahul        | D1           |
| 102             | Priya        | D2           |
| 103             | Amit         | D1           |

Department table

| Dept_ID (PK) | Dept_Name        |
| ------------ | ---------------- |
| D1           | Computer Science |
| D2           | Mathematics      |

SQL example:

```
CREATE TABLE Department (
    Dept_ID VARCHAR(10) PRIMARY KEY,
    Dept_Name VARCHAR(100)
);

CREATE TABLE Student (
    Student_ID INT PRIMARY KEY,
    Student_Name VARCHAR(50),
    Dept_ID VARCHAR(10),
    FOREIGN KEY (Dept_ID) REFERENCES Department(Dept_ID)
);
```

Remember: No transitive dependency of non-key attributes on a candidate key.

### Boyce-Codd Normal Form (BCNF)
A stricter version of 3NF where for every functional dependency `A → B`, A must be a super key. Handles certain anomalies that 3NF does not.

Rule: For every non-trivial functional dependency `X → Y`, X must be a superkey.

BCNF is stricter than 3NF.

Consider the following table:

| Student | Subject | Teacher |
| ------- | ------- | ------- |
| Rahul   | DBMS    | Mr. Das |
| Priya   | DBMS    | Mr. Das |
| Rahul   | SQL     | Ms. Roy |

Assume these rules:

- A student can study many subjects.
- Each teacher teaches only one subject.
- A subject may be taught by multiple teachers.

The functional dependency is:

`Teacher → Subject`

If `Teacher` is not a superkey, this dependency violates BCNF.

Decompose the table into:

Teacher_Subject

| Teacher (PK) | Subject |
| ------------ | ------- |
| Mr. Das      | DBMS    |
| Ms. Roy      | SQL     |

Student_Teacher

| Student | Teacher |
| ------- | ------- |
| Rahul   | Mr. Das |
| Priya   | Mr. Das |
| Rahul   | Ms. Roy |

The decomposition separates the teacher's subject assignment from the student-teacher relationship. Under the stated assumptions, this removes the BCNF violation.

Remember: Every determinant must be a superkey.

### Fourth Normal Form (4NF)
Removes multivalued dependencies — a table should not contain two or more independent multivalued facts about an entity.

### Multivalued Dependency (MVD)

A dependency where one attribute determines a set of values for another attribute, independent of other attributes in the relation — the basis for 4NF violations.

### Fifth Normal Form (5NF / PJNF)

A table is in 5NF if it is in 4NF and cannot be decomposed into smaller tables without loss of data (i.e., it has no join dependency that isn't implied by candidate keys).

### Lossless Decomposition and Dependency Preservation

- **Lossless Join Decomposition**: Joining the decomposed tables back together must reproduce the original relation exactly, with no spurious tuples.
- **Dependency Preservation**: All functional dependencies of the original relation must still be enforceable without needing a join.

### Quick comparison of normal forms

| Normal Form | Main rule                                      | Problem removed                         |
| ----------- | ---------------------------------------------- | --------------------------------------- |
| 1NF         | Atomic values                                  | Multivalued cells                       |
| 2NF         | No partial dependency                          | Dependency on part of a composite key   |
| 3NF         | No transitive dependency of non-key attributes | Indirect dependency                     |
| BCNF        | Every determinant is a superkey                | Remaining determinant-related anomalies |

## Denormalization

The intentional process of introducing redundancy into a normalized database to improve read performance, often used in reporting or analytics systems.

---

# Structured Query Language (SQL)

## SQL Data Types

| **Data Type**            | **Used For**          | **Example**               |
| ------------------------ | --------------------- | ------------------------- |
| `INT`                    | Whole numbers         | `25`                      |
| `DECIMAL`                | Exact decimal numbers | `99.50`                   |
| `CHAR(n)`                | Fixed-length text     | `'ABC'`                   |
| `VARCHAR(n)`             | Variable-length text  | `'John'`                  |
| `DATE`                   | Date                  | `'2026-09-15'`            |
| `TIME`                   | Time                  | `'12:30:00'`              |
| `DATETIME` / `TIMESTAMP` | Date + time           | `'2026-09-15 12:30:00'`   |
| `BOOLEAN`                | True/False            | `TRUE`                    |
| `TEXT`                   | Large text            | `'A long description...'` |

**Numeric:** `INT`, `DECIMAL`, `FLOAT`
**String:** `CHAR`, `VARCHAR`, `TEXT`
**Date & Time:** `DATE`, `TIME`, `DATETIME`
**Boolean:** `BOOLEAN`
**Binary:** `BLOB` (images/files, etc.)

### Example

```sql
CREATE TABLE Student (
    id INT,
    name VARCHAR(50),
    marks DECIMAL(5,2),
    birth_date DATE,
    active BOOLEAN
);
```

## NULL and Comparisons

`NULL` represents a missing or unknown value. It cannot be compared using `=` or `<>`.

Wrong:

```sql
WHERE salary = NULL
```

Correct:

```sql
WHERE salary IS NULL
```

To find values that are not NULL:

```sql
WHERE salary IS NOT NULL
```

**Remember:** `NULL` is not equal to anything, so use `IS NULL` and `IS NOT NULL` instead of `= NULL` or `<> NULL`.

## Categories of SQL Commands

### DDL (Data Definition Language)
Defines database structure. Commands: `CREATE`, `ALTER`, `DROP`, `TRUNCATE`, `RENAME`.

**CREATE** — defines a new table, view, index, or database object.
```sql
CREATE TABLE Employee (
    EmpID INT PRIMARY KEY,
    Name VARCHAR(50) NOT NULL,
    DeptID INT,
    Salary DECIMAL(10,2)
);
```

**ALTER** — modifies an existing table structure (add/drop/modify columns).
```sql
ALTER TABLE Employee ADD Email VARCHAR(100);
ALTER TABLE Employee MODIFY Salary DECIMAL(12,2);
ALTER TABLE Employee DROP COLUMN Email;
```

**DROP** — permanently removes a table/object and its data from the database.
```sql
DROP TABLE Employee;
```

**TRUNCATE** — removes all rows from a table quickly, but keeps the table structure. Cannot be rolled back in most DBMSs (unlike `DELETE`) since it doesn't log individual row deletions.
```sql
TRUNCATE TABLE Employee;
```

**RENAME** — renames a database object.
```sql
RENAME TABLE Employee TO Staff;
```

**Pattern to solve DDL questions:** If the question mentions *"structure"*, *"schema"*, *"remove all data but keep the table"*, or *"add/remove a column"* — it's DDL. `TRUNCATE` vs `DELETE` is a classic trick: TRUNCATE = fast, no WHERE clause, resets structure only; DELETE = row-by-row, logged, supports WHERE, can be rolled back.

---

### DML (Data Manipulation Language)
Manipulates data within tables. Commands: `SELECT`, `INSERT`, `UPDATE`, `DELETE`.

**SELECT** — retrieves data from one or more tables.
```sql
SELECT Name, Salary FROM Employee WHERE DeptID = 3;
```

**INSERT** — adds new rows to a table.
```sql
INSERT INTO Employee (EmpID, Name, DeptID, Salary)
VALUES (101, 'Asha Roy', 3, 55000);
```

**UPDATE** — modifies existing rows.
```sql
UPDATE Employee SET Salary = Salary * 1.10 WHERE DeptID = 3;
```

**DELETE** — removes rows matching a condition (logged, rollback-able, supports WHERE).
```sql
DELETE FROM Employee WHERE EmpID = 101;
```

**Pattern to solve DML questions:** These questions ask you to *change data*, not structure. If a WHERE clause is missing on UPDATE/DELETE, flag it — that's the most common "what could go wrong here" trap (it affects **all** rows).

---

### DCL (Data Control Language)
Controls access permissions. Commands: `GRANT`, `REVOKE`.

**GRANT** — gives a user specific privileges.
```sql
GRANT SELECT, INSERT ON Employee TO 'analyst_user';
```

**REVOKE** — removes previously granted privileges.
```sql
REVOKE INSERT ON Employee FROM 'analyst_user';
```

**Pattern to solve DCL questions:** Look for keywords like *"permission"*, *"access control"*, *"authorize a user"* — always `GRANT`/`REVOKE`, never DML.

---

### TCL (Transaction Control Language)
Manages transactions. Commands: `COMMIT`, `ROLLBACK`, `SAVEPOINT`.

**COMMIT** — permanently saves all changes made in the current transaction.
```sql
BEGIN TRANSACTION;
UPDATE Employee SET Salary = Salary + 5000 WHERE EmpID = 101;
COMMIT;
```

**ROLLBACK** — undoes changes made in the current transaction (back to last commit or savepoint).
```sql
BEGIN TRANSACTION;
UPDATE Employee SET Salary = 0 WHERE EmpID = 101;
ROLLBACK;
```

**SAVEPOINT** — sets a named point within a transaction to roll back to, without undoing the whole transaction.
```sql
BEGIN TRANSACTION;
UPDATE Employee SET Salary = Salary + 1000 WHERE DeptID = 1;
SAVEPOINT sp1;
UPDATE Employee SET Salary = Salary - 500 WHERE DeptID = 2;
ROLLBACK TO sp1;
COMMIT;
```

**Pattern to solve TCL questions:** These pair naturally with ACID scenario questions (see Question 4 below). If a scenario says *"undo part of a transaction but keep the rest"*, that's `SAVEPOINT` + partial `ROLLBACK`.

## Joins

### Inner Join
Returns only the rows that have matching values in both tables.

### Left (Outer) Join
Returns all rows from the left table and matched rows from the right table; unmatched right-side columns are NULL.

### Right (Outer) Join
Returns all rows from the right table and matched rows from the left table; unmatched left-side columns are NULL.

### Full Outer Join
Returns all rows from both tables, with NULLs where no match exists on either side.

### Self Join
A table is joined with itself, typically used to compare rows within the same table (e.g., finding employees and their managers in the same Employee table).

### Cross Join
Produces the Cartesian product of two tables — every row of one table combined with every row of the other.


### Join Syntax Examples

```sql
-- Inner Join
SELECT e.Name, d.DeptName
FROM Employee e
INNER JOIN Department d ON e.DeptID = d.DeptID;

-- Left Join
SELECT e.Name, d.DeptName
FROM Employee e
LEFT JOIN Department d ON e.DeptID = d.DeptID;

-- Right Join
SELECT e.Name, d.DeptName
FROM Employee e
RIGHT JOIN Department d ON e.DeptID = d.DeptID;

-- Full Outer Join
SELECT e.Name, d.DeptName
FROM Employee e
FULL OUTER JOIN Department d ON e.DeptID = d.DeptID;

-- Self Join (employees and their managers)
SELECT emp.Name AS Employee, mgr.Name AS Manager
FROM Employee emp
JOIN Employee mgr ON emp.ManagerID = mgr.EmpID;

-- Cross Join
SELECT e.Name, p.ProjectName
FROM Employee e
CROSS JOIN Project p;
```

**Pattern to solve Join questions:** Ask "do I need unmatched rows kept?" — no → `INNER JOIN`; keep unmatched left rows → `LEFT JOIN`; keep unmatched right rows → `RIGHT JOIN`; keep unmatched from both → `FULL OUTER JOIN`. If the question compares rows *within the same table* (e.g., "employee and manager," "same city"), it's a `SELF JOIN`. If it asks for *every possible combination* with no condition, it's a `CROSS JOIN`.

## Subqueries

A query nested inside another query, used in `SELECT`, `WHERE`, or `FROM` clauses to filter or compute values based on another dataset.


**Types of Subqueries:**

- **Single-row subquery** — returns one row, used with `=`, `>`, `<`.
- **Multi-row subquery** — returns multiple rows, used with `IN`, `ANY`, `ALL`.
- **Correlated subquery** — references a column from the outer query; re-evaluated for each outer row.

```sql
-- Single-row subquery
SELECT Name FROM Employee
WHERE Salary = (SELECT MAX(Salary) FROM Employee);

-- Multi-row subquery
SELECT Name FROM Employee
WHERE DeptID IN (SELECT DeptID FROM Department WHERE Location = 'Kolkata');

-- Correlated subquery (employees earning above their department's average)
SELECT Name, Salary, DeptID FROM Employee e1
WHERE Salary > (
    SELECT AVG(Salary) FROM Employee e2 WHERE e2.DeptID = e1.DeptID
);
```

**Pattern to solve Subquery questions:** If the inner query's result depends on the outer query's current row (references outer table's column), it's **correlated** — it runs once per outer row. If it can run independently and returns a fixed set of values first, it's a plain (non-correlated) subquery. "Find employees earning more than the average of *their own* department" is the classic correlated-subquery signal.

## Aggregate Functions

Functions like `COUNT()`, `SUM()`, `AVG()`, `MIN()`, and `MAX()` that operate on a set of values and return a single summarized value, often used with `GROUP BY` and filtered using `HAVING`.


```sql
-- Total employees per department
SELECT DeptID, COUNT(*) AS TotalEmployees
FROM Employee
GROUP BY DeptID;

-- Departments with average salary above 50000
SELECT DeptID, AVG(Salary) AS AvgSalary
FROM Employee
GROUP BY DeptID
HAVING AVG(Salary) > 50000;
```

### ROUND with AVG

`ROUND(number, 2)` rounds a number to 2 decimal places.

```sql
ROUND(AVG(number), 2)
```

Example:

```sql
SELECT ROUND(AVG(Salary), 2) AS AvgSalary
FROM Employee;
```

This calculates the average salary and rounds the result to 2 decimal places.

**Pattern to solve Aggregate questions:** `WHERE` filters rows *before* grouping; `HAVING` filters groups *after* aggregation. If the condition involves an aggregate function (`COUNT`, `AVG`, `SUM`, etc.), it must go in `HAVING`, never `WHERE`.

## WHERE vs GROUP BY vs HAVING vs ORDER BY

These four clauses are easy to confuse. Remember:

* **WHERE** → filters individual rows
* **GROUP BY** → combines rows into groups
* **HAVING** → filters groups
* **ORDER BY** → sorts the final result

### Quick Mental Model

Think of a SQL query as a sequence:

```text
FROM
  ↓
WHERE       → Which individual rows do I want?
  ↓
GROUP BY    → Which rows should be put together?
  ↓
HAVING      → Which groups do I want to keep?
  ↓
SELECT      → What do I want to show?
  ↓
ORDER BY    → In what order should I show it?
```

### 1. WHERE — Filter Individual Rows

Use `WHERE` when you want to filter individual records.

```sql
SELECT Name, Salary
FROM Employee
WHERE Salary > 40000;
```

This asks:

> "Which employees have a salary greater than 40,000?"

Think:

**WHERE = filter rows**

---

### 2. GROUP BY — Create Groups

Use `GROUP BY` when you want to combine rows that have the same value and perform an aggregate calculation on each group.

```sql
SELECT DeptID, COUNT(*) AS EmployeeCount
FROM Employee
GROUP BY DeptID;
```

This asks:

> "How many employees are in each department?"

For example:

```text
DeptID = 1 → 3 employees
DeptID = 2 → 5 employees
DeptID = 3 → 2 employees
```

Think:

**GROUP BY = make groups**

You do **not** need `GROUP BY` simply because you are using `SELECT`.

For example:

```sql
SELECT employee_id
FROM Employees
WHERE salary < 30000
ORDER BY employee_id;
```

There is no grouping here, so `GROUP BY` is unnecessary.

---

### 3. HAVING — Filter Groups

Use `HAVING` when you want to filter the groups created by `GROUP BY`.

```sql
SELECT DeptID, COUNT(*) AS EmployeeCount
FROM Employee
GROUP BY DeptID
HAVING COUNT(*) >= 3;
```

This asks:

> "Which departments have at least 3 employees?"

The important distinction is:

```text
WHERE  → filters rows
HAVING → filters groups
```

You cannot normally use an aggregate condition such as:

```sql
WHERE COUNT(*) >= 3
```

because `COUNT(*)` is calculated after the rows have been grouped.

Instead use:

```sql
HAVING COUNT(*) >= 3
```

Think:

**HAVING = filter groups**

---

### 4. ORDER BY — Sort the Result

Use `ORDER BY` when you want to control the order in which the results are displayed.

```sql
SELECT employee_id, salary
FROM Employees
ORDER BY salary DESC;
```

This does not remove or combine any rows. It simply sorts them from highest salary to lowest salary.

```sql
ORDER BY salary ASC;
```

→ lowest to highest

```sql
ORDER BY salary DESC;
```

→ highest to lowest

Think:

**ORDER BY = sort**

---

### WHERE vs HAVING

This is the most important distinction to remember.

#### WHERE talks about an individual row

```sql
WHERE salary > 50000
```

Meaning:

> "Is this employee's salary greater than 50,000?"

#### HAVING talks about a group

```sql
HAVING COUNT(*) > 5
```

Meaning:

> "Does this department/group contain more than 5 employees?"

---

### All Four Together

Example:

> Find departments that have at least 3 employees earning at least $30,000, and display the departments with the highest average salary first.

```sql
SELECT DeptID, AVG(Salary) AS AvgSalary
FROM Employee
WHERE Salary >= 30000
GROUP BY DeptID
HAVING COUNT(*) >= 3
ORDER BY AvgSalary DESC;
```

Read it step by step:

```text
WHERE
→ Keep only employees earning at least $30,000.

GROUP BY
→ Put those employees into groups based on DeptID.

HAVING
→ Keep only departments containing at least 3 employees.

ORDER BY
→ Sort the remaining departments by average salary, highest first.
```

### The 4-Question Trick

Whenever you are confused, ask yourself:

```text
1. Am I filtering individual rows?
   → WHERE

2. Am I putting similar rows together?
   → GROUP BY

3. Am I filtering the groups?
   → HAVING

4. Am I only changing the order?
   → ORDER BY
```

### Easy Memory Trick

```text
WHERE     → FILTER ROWS
GROUP BY  → MAKE GROUPS
HAVING    → FILTER GROUPS
ORDER BY  → SORT
```

---

## LIKE and Wildcards

`LIKE` is used to search for a pattern in a string.

`%` means **zero or more characters**.

Examples:

```sql
SELECT * FROM Employee
WHERE Name LIKE 'A%';
```

→ Names starting with `A`

```sql
SELECT * FROM Employee
WHERE Name LIKE '%a';
```

→ Names ending with `a`

```sql
SELECT * FROM Employee
WHERE Name LIKE '%an%';
```

→ Names containing `an`

**Remember:** `%` can match zero, one, or many characters.

---

## REGEX and REGEXP_LIKE

`REGEXP_LIKE` is used to check whether a string matches a regular-expression pattern.

Example:

```sql
REGEXP_LIKE(email, '^[A-Za-z][A-Za-z0-9_.-]*@gmail[.]com$')
```

| **Pattern**       | **Meaning**                        |
| ----------------- | ---------------------------------- |
| `[A-Za-z]`        | One letter, uppercase or lowercase |
| `[A-Za-z0-9_.-]*` | Zero or more allowed characters    |
| `^`               | Start of the string                |
| `@gmail[.]com`    | Exactly `@gmail.com`               |
| `$`               | End of the string                  |

Example:

```sql
SELECT *
FROM Student
WHERE REGEXP_LIKE(email, '^[A-Za-z][A-Za-z0-9_.-]*@gmail[.]com$');
```

---

## String Functions

### SUBSTR

```sql
SUBSTR(A, index, length)
```

`SUBSTR(name, 1, 1)` extracts the first character.

`SUBSTR(name, 2)` gets the rest of the name.

### UPPER

`UPPER(...)` converts text to uppercase.

### LOWER

`LOWER(...)` converts text to lowercase.

### CONCAT

`CONCAT(...)` combines two or more strings.

Example:

```sql
CONCAT(
    UPPER(SUBSTR(name, 1, 1)),
    LOWER(SUBSTR(name, 2))
)
```

This converts a name so that the first character is uppercase and the remaining characters are lowercase.

---

## Practice Questions

Use the following `Employees` table:

```text
+-------------+-----------+------------+--------+
| employee_id | name      | department | salary |
+-------------+-----------+------------+--------+
| 1           | Alice     | IT         | 60000  |
| 2           | Bob       | IT         | 40000  |
| 3           | Charlie   | HR         | 35000  |
| 4           | David     | HR         | 25000  |
| 5           | Eva       | Sales      | 50000  |
| 6           | Frank     | Sales      | 30000  |
| 7           | Grace     | Sales      | 25000  |
| 8           | Henry     | IT         | 70000  |
+-------------+-----------+------------+--------+
```

### Practice 1 — WHERE

Return the `name` and `salary` of employees whose salary is greater than `$40,000`.

**Question to ask:** Am I filtering individual employees or creating groups?

---

### Practice 2 — ORDER BY

Return all employees, sorted by salary from highest to lowest.

**Question to ask:** Am I filtering or grouping anything, or do I only need to change the order?

---

### Practice 3 — GROUP BY

Find the number of employees in each department.

Expected result:

```text
department | employee_count
-----------+---------------
HR         | ?
IT         | ?
Sales      | ?
```

**Question to ask:** Do I want one result for each department?

---

### Practice 4 — HAVING

Find the departments that have at least 3 employees.

Expected result:

```text
department | employee_count
-----------+---------------
IT         | 3
Sales      | 3
```

**Question to ask:** Am I filtering individual employees or filtering groups?

---

### Practice 5 — WHERE + GROUP BY + HAVING + ORDER BY

Find departments whose average salary is greater than `$40,000`, considering only employees whose salary is at least `$30,000`.

Return the department and average salary, ordered by average salary from highest to lowest.

Before writing the query, identify what each clause should do:

```text
WHERE     → ?
GROUP BY  → ?
HAVING    → ?
ORDER BY  → ?
```

### Final Cheat Sheet

| Clause     | Main job               | Think              |
| ---------- | ---------------------- | ------------------ |
| `WHERE`    | Filter individual rows | **Which rows?**    |
| `GROUP BY` | Create groups          | **Group by what?** |
| `HAVING`   | Filter groups          | **Which groups?**  |
| `ORDER BY` | Sort results           | **What order?**    |

## Views

A virtual table based on the result of a stored SQL query. Views do not store data themselves (in most cases) but simplify complex queries and add a layer of abstraction and security.


```sql
-- Create a view
CREATE VIEW HighEarners AS
SELECT Name, DeptID, Salary
FROM Employee
WHERE Salary > 80000;

-- Query a view like a table
SELECT * FROM HighEarners;

-- Update a view's definition
CREATE OR REPLACE VIEW HighEarners AS
SELECT Name, DeptID, Salary
FROM Employee
WHERE Salary > 90000;

-- Drop a view
DROP VIEW HighEarners;
```

**Pattern to solve View questions:** Reach for a view when a question mentions *"hide certain columns from some users,"* *"simplify a repeated complex query,"* or *"restrict access to a subset of rows/columns"* without duplicating data.

## Indexes

A database object that improves the speed of data retrieval operations on a table at the cost of additional storage and slower write operations.

- **Clustered Index**: Determines the physical order of data in a table; a table can have only one
- **Non-Clustered Index**: A separate structure that holds pointers to the actual data; a table can have several

---

# Transactions and Concurrency Control

## What is a Transaction?

A transaction is a logical unit of work that consists of one or more operations, which must be executed as a whole — either all operations succeed or none do.

## Transaction States

A transaction moves through the following states during execution:

1. **Active**: Initial state; the transaction is executing.
2. **Partially Committed**: After the final statement executes, before changes are permanently saved.
3. **Committed**: Transaction completed successfully; changes are permanent.
4. **Failed**: Normal execution can no longer proceed.
5. **Aborted**: Transaction rolled back and database restored to its pre-transaction state.

## ACID Properties

### Atomicity
Ensures that a transaction is treated as a single indivisible unit — it either completes entirely or has no effect at all.

### Consistency
Ensures that a transaction brings the database from one valid state to another, maintaining all defined rules and constraints.

### Isolation
Ensures that concurrently executing transactions do not interfere with each other, as if they were executed sequentially.

### Durability
Ensures that once a transaction is committed, its changes are permanently saved, even in the event of a system failure.

## Concurrency Problems

### Dirty Read
A transaction reads data that has been modified by another uncommitted transaction, which may later be rolled back.

### Non-Repeatable Read
A transaction reads the same row twice and gets different values because another transaction modified and committed changes in between.

### Phantom Read
A transaction re-executes a query and finds new rows that were inserted by another committed transaction.

## Isolation Levels

| Level | Dirty Read | Non-Repeatable Read | Phantom Read |
|---|---|---|---|
| Read Uncommitted | Possible | Possible | Possible |
| Read Committed | Prevented | Possible | Possible |
| Repeatable Read | Prevented | Prevented | Possible |
| Serializable | Prevented | Prevented | Prevented |

## Concurrency Control Techniques

### Lock-Based Protocols
Transactions must acquire locks (shared or exclusive) on data items before accessing them, preventing conflicting operations from occurring simultaneously.

### Two-Phase Locking (2PL)
A protocol where a transaction acquires all necessary locks before releasing any (growing phase), and releases locks only after (shrinking phase), ensuring serializability.

### Timestamp Ordering
Transactions are ordered based on timestamps, ensuring conflicting operations execute according to their timestamp order.

### Deadlock
A situation where two or more transactions wait indefinitely for locks held by each other. Handled through prevention, detection, and recovery strategies such as wait-die, wound-wait, or timeout mechanisms.

---

# Database Recovery and Backup

## Log-Based Recovery

The DBMS maintains logs of every transaction operation. In case of failure, these logs are used to redo committed transactions and undo incomplete ones.

## Checkpoints

A point in time recorded in the log where the database state is saved, allowing recovery processes to avoid redoing the entire transaction history from the beginning.

## Types of Backup

- **Full Backup**: Complete copy of the entire database
- **Incremental Backup**: Copies only changes made since the last backup
- **Differential Backup**: Copies all changes made since the last full backup

---

# Storage and File Organization

## File Organization Methods

### Heap File Organization
Records are stored in no particular order, making insertion fast but search operations slow (linear scan).

### Sequential File Organization
Records are stored in sorted order based on a key field, enabling efficient range queries but slower insertions.

### Hash File Organization
A hash function maps key values to specific storage locations, enabling fast direct lookups but poor performance for range queries.

### Clustered File Organization
Related records from one or more tables are stored physically close together to speed up retrieval of associated data.

## Indexing Structures

### B-Tree / B+ Tree
Balanced tree structures widely used for indexing, allowing efficient insertion, deletion, and range queries in logarithmic time. B+ Trees store all actual data at leaf nodes, linked sequentially for fast range scans.

### Hash Index
Uses a hash function to map keys to bucket locations, offering very fast equality lookups but no support for range queries.

---

# Distributed Databases and NoSQL

## Distributed Database

A database in which data is stored across multiple physical locations, either on multiple computers in the same location or spread across a network, allowing for improved availability and scalability.

## CAP Theorem

States that a distributed system can only guarantee two out of the following three properties simultaneously:

- **Consistency**: Every read receives the most recent write
- **Availability**: Every request receives a response, without guarantee of the latest data
- **Partition Tolerance**: The system continues to operate despite network partitions

## SQL vs NoSQL

| Aspect | SQL (Relational) | NoSQL (Non-Relational) |
|---|---|---|
| Schema | Fixed, predefined | Dynamic, flexible |
| Scalability | Vertical (mostly) | Horizontal |
| Data Model | Tables | Document, Key-Value, Column, Graph |
| Transactions | Strong ACID support | Often eventual consistency |
| Use Case | Structured, relational data | Big data, unstructured/semi-structured data |

## Types of NoSQL Databases

- **Document-Based**: Stores data as JSON-like documents (e.g., MongoDB)
- **Key-Value Store**: Stores data as simple key-value pairs (e.g., Redis)
- **Column-Family Store**: Stores data in columns rather than rows (e.g., Cassandra)
- **Graph Database**: Stores data as nodes and edges, ideal for relationship-heavy data (e.g., Neo4j)

> MongoDB (document store) and Redis (key-value / in-memory store) are the two NoSQL systems you are most likely to meet first. The next two chapters cover them in depth.

---

# MongoDB in Depth

## What is MongoDB?

MongoDB is an open-source, **document-oriented NoSQL database**. Instead of rows in tables, it stores data as flexible, JSON-like documents (internally stored in a binary format called **BSON**). Documents in the same collection do not need to share the same structure, which makes MongoDB a natural fit for evolving, semi-structured, or hierarchical data.

Key characteristics:

- **Document model**: One document can hold nested objects and arrays, so related data can live together.
- **Flexible (dynamic) schema**: Fields can be added without a migration; optional validation can still be enforced.
- **Rich query language**: Filters, projections, sorting, aggregation pipelines, text search, and geospatial queries.
- **Horizontal scalability**: Built-in sharding spreads data across servers.
- **High availability**: Replica sets provide automatic failover.
- **Indexing**: Secondary indexes of many kinds, not just on the primary key.

## SQL vs MongoDB Terminology

| SQL (Relational) | MongoDB | Notes |
|---|---|---|
| Database | Database | Same idea |
| Table | Collection | A group of documents |
| Row / Tuple | Document | A BSON object |
| Column / Attribute | Field | Can differ per document |
| Primary Key | `_id` | Auto-added and always unique |
| Foreign Key | Reference (stored `_id`) | Not enforced by the database |
| JOIN | Embedding or `$lookup` | Embedding avoids the join |
| `GROUP BY` | `$group` stage | Part of the aggregation pipeline |
| Index | Index | Same purpose |
| Transaction | Transaction | Single-document writes are always atomic |

## BSON and Data Types

BSON ("Binary JSON") extends JSON with more types and is designed for fast traversal and encoding.

| BSON Type | Example | Notes |
|---|---|---|
| String | `"Rahul"` | UTF-8 |
| Int32 / Int64 | `NumberInt(25)`, `NumberLong(9000000000)` | Whole numbers |
| Double | `99.5` | Default for JS numbers in the shell |
| Decimal128 | `NumberDecimal("19.99")` | Use for money |
| Boolean | `true` | |
| Date | `new Date()` | Stored as UTC milliseconds |
| ObjectId | `ObjectId("...")` | 12-byte unique identifier |
| Array | `["a", "b"]` | Can hold mixed types |
| Embedded document | `{ city: "Kolkata" }` | Nested object |
| Null | `null` | |
| Binary data | `BinData(...)` | Files, hashes |
| Regular expression | `/^A/` | |

**Document size limit:** A single document can be at most **16 MB**. Larger files use **GridFS**.

### The `_id` Field and ObjectId

Every document must have a unique `_id`. If you do not supply one, MongoDB generates an `ObjectId`:

```text
ObjectId = 4-byte timestamp + 5-byte random value + 3-byte incrementing counter   (12 bytes total)
```

Because it starts with a timestamp, ObjectIds are roughly sortable by creation time, and the creation time can be extracted from them. An `_id` can be any unique value (number, string, UUID) if you prefer.

## Core Architecture

| Component | Role |
|---|---|
| `mongod` | The database server process |
| `mongos` | Query router used in sharded clusters |
| `mongosh` | The modern interactive shell (JavaScript-based) |
| Config servers | Store cluster metadata in a sharded cluster |
| Replica set | A group of `mongod` processes holding the same data |
| Storage engine | Manages how data is stored (WiredTiger by default) |

**Hierarchy:** Deployment → Database → Collection → Document → Field.

> Databases and collections are created **lazily**: they appear the first time you insert data into them.

## Installing and Connecting

```bash
# Ubuntu (after adding the official MongoDB repository)
sudo apt install -y mongodb-org
sudo systemctl enable --now mongod

# Docker
docker run --name mongo-dev -p 27017:27017 -d mongo:7

# Connect with the shell
mongosh
mongosh "mongodb://username:password@hostname:27017/mydb"
```

Basic shell commands:

```javascript
show dbs                  // List databases
use shop                  // Switch to (or lazily create) the 'shop' database
show collections          // List collections in the current database
db.dropDatabase()         // Drop the current database
db.customers.drop()       // Drop one collection
db.stats()                // Database statistics
```

## CRUD Operations

### Create (Insert)

```javascript
db.students.insertOne({
  name: "Rahul",
  age: 21,
  courses: ["DBMS", "SQL"],
  address: { city: "Kolkata", pin: "700001" }
});

db.students.insertMany([
  { name: "Priya", age: 22, courses: ["Java", "Python"] },
  { name: "Amit",  age: 20, courses: ["DBMS"] }
]);
```

### Read (Query)

```javascript
db.students.find()                              // All documents
db.students.find({ age: 21 })                   // Equality filter
db.students.findOne({ name: "Rahul" })          // First match only
db.students.find({ age: { $gte: 21 } })         // Comparison operator
db.students.find({ "address.city": "Kolkata" }) // Dot notation for nested fields
db.students.find({ courses: "DBMS" })           // Array contains a value
```

**Projection** chooses which fields to return (`1` = include, `0` = exclude):

```javascript
db.students.find({ age: { $gte: 21 } }, { name: 1, age: 1, _id: 0 })
```

**Sorting, limiting, skipping, counting:**

```javascript
db.students.find().sort({ age: -1 })            // -1 = descending, 1 = ascending
db.students.find().sort({ age: -1 }).limit(5)   // Top 5
db.students.find().skip(10).limit(10)           // Pagination (slow for large skips)
db.students.countDocuments({ age: { $gte: 21 } })
db.students.distinct("courses")
```

### Query Operators

| Category | Operators | Example |
|---|---|---|
| Comparison | `$eq`, `$ne`, `$gt`, `$gte`, `$lt`, `$lte`, `$in`, `$nin` | `{ age: { $in: [20, 21] } }` |
| Logical | `$and`, `$or`, `$not`, `$nor` | `{ $or: [{ age: 20 }, { name: "Priya" }] }` |
| Element | `$exists`, `$type` | `{ email: { $exists: true } }` |
| Array | `$all`, `$elemMatch`, `$size` | `{ courses: { $all: ["DBMS", "SQL"] } }` |
| Evaluation | `$regex`, `$expr`, `$mod`, `$text` | `{ name: { $regex: /^A/ } }` |

`$elemMatch` is needed when several conditions must hold on the **same** array element:

```javascript
db.orders.find({ items: { $elemMatch: { sku: "A1", qty: { $gte: 2 } } } })
```

### Update

```javascript
db.students.updateOne({ name: "Rahul" }, { $set: { age: 22 } })
db.students.updateMany({ age: { $lt: 21 } }, { $inc: { age: 1 } })
db.students.replaceOne({ name: "Amit" }, { name: "Amit", age: 21 })   // Replaces the whole document

// Upsert: update if found, otherwise insert
db.students.updateOne({ name: "Neha" }, { $set: { age: 23 } }, { upsert: true })
```

| Update Operator | Purpose |
|---|---|
| `$set` / `$unset` | Set or remove a field |
| `$inc` / `$mul` | Increment or multiply a number |
| `$min` / `$max` | Update only if the new value is smaller or larger |
| `$rename` | Rename a field |
| `$currentDate` | Set a field to the current date |
| `$push` | Append to an array |
| `$addToSet` | Append only if not already present |
| `$pull` | Remove matching elements from an array |
| `$pop` | Remove the first (`-1`) or last (`1`) array element |

```javascript
db.students.updateOne({ name: "Rahul" }, { $push: { courses: "Java" } })
db.students.updateOne({ name: "Rahul" }, { $addToSet: { courses: "SQL" } })   // No duplicate
db.students.updateOne({ name: "Rahul" }, { $pull: { courses: "SQL" } })
```

> Always use an update operator such as `$set`. Passing a plain document to `updateOne` raises an error, and `replaceOne` replaces everything except `_id`.

### Delete

```javascript
db.students.deleteOne({ name: "Amit" })
db.students.deleteMany({ age: { $lt: 18 } })
db.students.deleteMany({})          // Removes all documents but keeps the collection and its indexes
```

**Safe habit (same as SQL):** Run `find()` with the same filter first to preview which documents will be affected.

## Schema Design and Data Modeling

MongoDB has a flexible schema, but **modeling still matters**. The central question is: **embed or reference?**

### Embedding (Denormalized)

Store related data inside one document.

```javascript
{
  _id: 1,
  name: "Rahul",
  addresses: [
    { type: "home", city: "Kolkata" },
    { type: "office", city: "Howrah" }
  ]
}
```

- Reads are fast: one query returns everything.
- Updates to one document are atomic.
- Best for data that is **read together**, has a **bounded size**, and belongs to one parent.

### Referencing (Normalized)

Store the related data in a separate collection and keep its `_id`.

```javascript
// customers
{ _id: 101, name: "Rahul" }
// orders
{ _id: 9001, customer_id: 101, total: 1500 }
```

- Avoids duplication and unbounded document growth.
- Needs a second query or a `$lookup` to combine data.
- Best for **many-to-many** relationships, large or frequently changing child data, or data accessed independently.

### Embed vs Reference

| Factor | Prefer Embedding | Prefer Referencing |
|---|---|---|
| Relationship | One-to-one, one-to-few | One-to-many (very many), many-to-many |
| Access pattern | Data read together | Data accessed on its own |
| Growth | Bounded | Unbounded |
| Update frequency | Rarely changes | Changes often, shared by many parents |
| Atomicity need | Single-document atomicity is enough | Multi-document consistency handled otherwise |

**Rule of thumb:** *Data that is accessed together should be stored together.*

### Modeling Relationships

| Relationship | Typical Design |
|---|---|
| One-to-One | Embed (for example, profile inside user) |
| One-to-Few | Embed an array (for example, a few addresses) |
| One-to-Many | Reference from the "many" side (for example, `customer_id` in each order) |
| One-to-Squillions | Reference from the child side only; never store a huge array in the parent |
| Many-to-Many | Arrays of references on one or both sides, or a junction collection |

### Common Design Patterns

| Pattern | Idea |
|---|---|
| Subset | Keep only the most-used part (for example, the latest 10 reviews) inside the parent; store the rest elsewhere |
| Bucket | Group time-series or event data into one document per time window |
| Computed | Pre-compute values (totals, counts) and store them to avoid repeated work |
| Extended Reference | Copy a few frequently needed fields of the referenced document (for example, customer name) into the child |
| Polymorphic | Store different shaped documents in one collection, distinguished by a `type` field |
| Schema Versioning | Add a `schema_version` field so old and new shapes can coexist during migration |

### Anti-Patterns to Avoid

- **Unbounded arrays** that grow forever (documents hit the 16 MB limit and become slow).
- **Very large documents** when only a small part is used per request.
- **Too many collections** or too many indexes.
- **Treating MongoDB like a relational database** with excessive references and many `$lookup` calls.
- **Storing money as floating-point**; use `Decimal128` or integer minor units.

### Schema Validation

Flexible does not mean unvalidated. A collection can enforce rules with `$jsonSchema`:

```javascript
db.createCollection("students", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["name", "age"],
      properties: {
        name: { bsonType: "string" },
        age:  { bsonType: "int", minimum: 0, maximum: 120 }
      }
    }
  },
  validationLevel: "strict",
  validationAction: "error"
});
```

## Indexes

Without an index, MongoDB performs a **collection scan** (`COLLSCAN`), reading every document. Indexes are stored as B-Tree structures and let queries use an **index scan** (`IXSCAN`).

```javascript
db.students.createIndex({ name: 1 })                 // Single-field ascending
db.students.createIndex({ age: 1, name: 1 })         // Compound index
db.students.createIndex({ email: 1 }, { unique: true })
db.students.getIndexes()
db.students.dropIndex("name_1")
```

| Index Type | Description | Typical Use |
|---|---|---|
| Single field | Index on one field | Simple lookups |
| Compound | Index on multiple fields (order matters) | Queries filtering and sorting on several fields |
| Multikey | Automatically created for array fields | Searching inside arrays |
| Text | Full-text search on strings | Basic keyword search |
| Geospatial (`2dsphere`) | Location queries | "Find restaurants within 2 km" |
| Hashed | Hash of a field's value | Hash-based sharding, equality lookups |
| TTL | Deletes documents automatically after a time | Sessions, logs, temporary data |
| Unique | Rejects duplicate values | Emails, usernames |
| Partial | Indexes only documents matching a filter | Smaller index for a subset |
| Sparse | Indexes only documents that have the field | Optional fields |
| Wildcard | Indexes fields not known in advance | Highly variable schemas |

```javascript
// TTL: remove documents 3600 seconds after 'createdAt'
db.sessions.createIndex({ createdAt: 1 }, { expireAfterSeconds: 3600 })

// Geospatial
db.places.createIndex({ location: "2dsphere" })

// Partial
db.orders.createIndex({ status: 1 }, { partialFilterExpression: { status: "OPEN" } })
```

### Compound Index and the ESR Rule

For a compound index, order the fields as:

```text
E → Equality fields first
S → Sort fields next
R → Range fields last
```

A compound index on `{ a: 1, b: 1, c: 1 }` supports queries on `a`, on `a + b`, and on `a + b + c` (the **prefix** rule), but not efficiently on `b` or `c` alone.

### Checking a Query with `explain()`

```javascript
db.students.find({ name: "Rahul" }).explain("executionStats")
```

| Clue in the output | Meaning |
|---|---|
| `COLLSCAN` | Full collection scan, usually a missing index |
| `IXSCAN` | An index is used |
| `totalDocsExamined` much larger than `nReturned` | The query reads many documents to return few; index is missing or poor |
| `FETCH` after `IXSCAN` | Index found the documents, then they were read from disk |
| Covered query (no `FETCH`) | All needed fields are in the index; fastest |

> Every extra index speeds up reads but slows writes and uses memory and disk. Index what you query.

## Aggregation Framework

The aggregation pipeline processes documents through ordered **stages**, where the output of one stage is the input of the next. It is MongoDB's equivalent of `GROUP BY`, joins, and reporting queries.

| Stage | Purpose | SQL Analogy |
|---|---|---|
| `$match` | Filter documents | `WHERE` / `HAVING` |
| `$group` | Group and aggregate | `GROUP BY` |
| `$project` | Choose or compute fields | `SELECT` |
| `$sort` | Sort documents | `ORDER BY` |
| `$limit` / `$skip` | Restrict results | `LIMIT` / `OFFSET` |
| `$unwind` | Split an array into one document per element | Flattening a one-to-many |
| `$lookup` | Left outer join with another collection | `LEFT JOIN` |
| `$addFields` / `$set` | Add computed fields | Computed column |
| `$count` | Count documents | `COUNT(*)` |
| `$facet` | Run several pipelines on the same input | Multiple reports at once |
| `$out` / `$merge` | Write results to a collection | `INSERT ... SELECT` |

Example: total spend and order count per customer, for completed orders, highest spenders first.

```javascript
db.orders.aggregate([
  { $match: { status: "COMPLETED" } },
  { $group: {
      _id: "$customer_id",
      totalSpent: { $sum: "$total" },
      orderCount: { $sum: 1 },
      avgOrder:   { $avg: "$total" }
  }},
  { $match: { orderCount: { $gte: 3 } } },     // Like HAVING
  { $sort: { totalSpent: -1 } },
  { $limit: 10 }
]);
```

Example of `$lookup` (join) and `$unwind`:

```javascript
db.orders.aggregate([
  { $lookup: {
      from: "customers",
      localField: "customer_id",
      foreignField: "_id",
      as: "customer"
  }},
  { $unwind: "$customer" },
  { $project: { _id: 0, orderId: "$_id", total: 1, customerName: "$customer.name" } }
]);
```

**Pipeline performance tips:**

- Put `$match` and `$limit` as early as possible so later stages process fewer documents.
- A `$match` or `$sort` at the very start of a pipeline can use an index.
- Use `$project` to drop unneeded fields early.
- Heavy `$lookup` use is often a sign that the schema should embed more.

## Transactions

- **Single-document operations are always atomic**, even when they modify several fields or nested documents. This is why embedding is encouraged.
- **Multi-document ACID transactions** are supported on replica sets (since MongoDB 4.0) and sharded clusters (since 4.2).

```javascript
const session = db.getMongo().startSession();
session.startTransaction({
  readConcern: { level: "snapshot" },
  writeConcern: { w: "majority" }
});

try {
  const accounts = session.getDatabase("bank").accounts;
  accounts.updateOne({ _id: "A" }, { $inc: { balance: -500 } });
  accounts.updateOne({ _id: "B" }, { $inc: { balance: 500 } });
  session.commitTransaction();
} catch (e) {
  session.abortTransaction();
  throw e;
} finally {
  session.endSession();
}
```

Things to remember:

- Transactions have a default time limit (60 seconds) and add overhead; keep them short.
- If a design needs transactions everywhere, reconsider whether the data model fits.
- Transactions require a replica set; a standalone server does not support them.

## Replication (High Availability)

A **replica set** is a group of `mongod` servers that keep identical copies of the data.

```text
          Client application
                 |
        +-----------------+
        |     Primary     |   ← accepts all writes
        +-----------------+
          /             \
 +-----------+     +-----------+
 | Secondary |     | Secondary |   ← replicate the primary's oplog
 +-----------+     +-----------+
```

| Concept | Meaning |
|---|---|
| Primary | The only member that accepts writes |
| Secondary | Copies the primary's data; can serve reads if allowed |
| Arbiter | Votes in elections but holds no data |
| Oplog | A capped collection recording all write operations; secondaries replay it |
| Election | If the primary fails, the remaining members vote for a new primary (needs a majority) |
| Voting members | Use an **odd** number of voting members so a majority is always possible |

### Write Concern

Controls how many members must acknowledge a write before it is reported successful.

| Setting | Meaning |
|---|---|
| `w: 1` | Only the primary acknowledges (fast, can lose the write on failover) |
| `w: "majority"` | A majority of members have the write (safe against failover) |
| `j: true` | The write has been written to the on-disk journal |

### Read Concern

| Level | Meaning |
|---|---|
| `local` | Returns the node's latest data, which may be rolled back later |
| `majority` | Returns only data acknowledged by a majority |
| `snapshot` | Consistent snapshot, used in transactions |
| `linearizable` | Reflects all majority-acknowledged writes before the read (slowest) |

### Read Preference

| Mode | Behaviour |
|---|---|
| `primary` (default) | Read only from the primary; always the latest data |
| `primaryPreferred` | Primary if available, otherwise a secondary |
| `secondary` / `secondaryPreferred` | Read from secondaries; may return slightly stale data |
| `nearest` | Lowest network latency |

## Sharding (Horizontal Scaling)

**Sharding** splits a large collection across multiple servers (**shards**), each of which is itself a replica set.

```text
  Application
       |
   +--------+   +--------+       +----------------+
   | mongos |   | mongos |  ←→   | Config servers |   (cluster metadata)
   +--------+   +--------+       +----------------+
     /     |         \
 Shard 1  Shard 2   Shard 3        (each shard is a replica set)
```

| Component | Role |
|---|---|
| Shard | Holds a subset of the data |
| `mongos` | Router that sends queries to the correct shard(s) |
| Config servers | Store which data (chunk) lives on which shard |
| Chunk | A contiguous range of shard-key values (default maximum around 128 MB) |
| Balancer | Moves chunks between shards to keep them even |

### Shard Key

The **shard key** decides how documents are distributed. It is the most important sharding decision and is hard to change later.

| Strategy | How it works | Good for | Weakness |
|---|---|---|---|
| Ranged | Contiguous ranges of key values | Range queries | Hot spots if the key increases steadily (for example, timestamps) |
| Hashed | Hash of the key value | Even write distribution | Range queries hit many shards |
| Zoned | Ranges tied to specific shards | Data locality, regional rules | More operational complexity |

A good shard key has:

- **High cardinality** (many distinct values).
- **Low frequency** (no single value dominating).
- **Non-monotonic change** (not always increasing), or is hashed.
- Appears in **most queries** so they target one shard instead of all (a "scatter-gather" query is slow).

> Do not shard until you need to. Replica sets, good indexes, and schema design solve most performance problems first.

## Storage Engine: WiredTiger

WiredTiger is the default storage engine.

| Feature | Description |
|---|---|
| Document-level concurrency | Multiple writers can modify different documents at once |
| Compression | Snappy (default), zstd, or zlib for collections; prefix compression for indexes |
| Journaling | Write-ahead log protects against crashes between checkpoints |
| Checkpoints | Periodic consistent snapshots of data on disk (about every 60 seconds) |
| Cache | Internal cache defaults to roughly 50% of (RAM minus 1 GB), with a 256 MB minimum |

## CAP Theorem Position

By default MongoDB favors **Consistency and Partition Tolerance (CP)**: all writes go to one primary, and if the primary becomes unreachable, writes pause until an election finishes. Reading from secondaries trades some consistency for availability and read scaling.

## Security

| Area | Practice |
|---|---|
| Authentication | Enable `--auth` (SCRAM by default); never run an open instance |
| Authorization | Role-Based Access Control (RBAC) with built-in and custom roles |
| Network | Bind to private interfaces only (`bindIp`), use firewalls and private networks |
| Encryption in transit | TLS for client and inter-node traffic |
| Encryption at rest | Storage-level or enterprise/Atlas encryption |
| Field-level encryption | Client-Side Field Level Encryption for highly sensitive fields |
| Auditing | Log access and changes (enterprise/Atlas feature) |

```javascript
use admin
db.createUser({
  user: "appUser",
  pwd: passwordPrompt(),             // Prompts instead of putting the password in history
  roles: [{ role: "readWrite", db: "shop" }]
})
```

> A MongoDB instance exposed to the internet without authentication is one of the most common causes of large data leaks. Always enable authentication and restrict network access.

## Backup and Restore

| Tool / Method | Purpose |
|---|---|
| `mongodump` / `mongorestore` | Logical backup and restore of BSON data |
| `mongoexport` / `mongoimport` | Export and import JSON or CSV (loses some type information) |
| Filesystem snapshots | Fast backups of the data directory (needs consistency, such as journaling on one volume) |
| Oplog-based backup | Enables point-in-time recovery |
| Atlas Backup | Managed continuous backup in MongoDB Atlas |

```bash
mongodump --uri="mongodb://localhost:27017/shop" --out=/backups/shop_2026_10_26
mongorestore --uri="mongodb://localhost:27017" /backups/shop_2026_10_26
```

## Other Notable Features

| Feature | Description |
|---|---|
| Change Streams | Subscribe to real-time changes on a collection, database, or cluster (built on the oplog) |
| GridFS | Stores files larger than 16 MB by splitting them into chunks |
| Time Series Collections | Optimized storage and queries for timestamped measurements |
| Capped Collections | Fixed-size collections that overwrite the oldest data (like a circular log) |
| Atlas Search / Vector Search | Full-text and semantic search inside Atlas |
| MongoDB Atlas | Fully managed cloud service on AWS, Azure, and Google Cloud |

## Using MongoDB from Application Code

```python
from pymongo import MongoClient

client = MongoClient("mongodb://localhost:27017")
db = client["shop"]

db.customers.insert_one({"name": "Asha", "city": "Kolkata"})

for doc in db.customers.find({"city": "Kolkata"}, {"_id": 0, "name": 1}):
    print(doc)

db.customers.update_one({"name": "Asha"}, {"$set": {"city": "Howrah"}})
db.customers.delete_one({"name": "Asha"})
```

Common drivers exist for Python (PyMongo), Node.js, Java, C#, Go, and more. Popular object-document mappers (ODMs) include Mongoose (Node.js) and Spring Data MongoDB (Java).

> Injection is possible in MongoDB too. Never pass raw user-supplied objects as a query filter, since an input such as `{ "$ne": null }` can bypass checks. Validate input types and sanitize operator keys.

## Monitoring and Troubleshooting

| Tool / Command | Purpose |
|---|---|
| `db.serverStatus()` | Overall server metrics |
| `db.currentOp()` | Currently running operations |
| `db.collection.explain()` | Query plan analysis |
| Database profiler (`db.setProfilingLevel`) | Records slow operations |
| `rs.status()` | Replica set health, member states, and lag |
| `sh.status()` | Sharded cluster layout |
| MongoDB Compass / Atlas Charts / Ops Manager | GUI and monitoring dashboards |

## MongoDB vs Relational Databases

| Aspect | Relational (SQL) | MongoDB |
|---|---|---|
| Schema | Fixed tables | Flexible documents |
| Relationships | Foreign keys and joins | Embedding or references |
| Joins | First-class, efficient | `$lookup`, used sparingly |
| Transactions | Strong ACID across tables | ACID within a document; multi-document available with overhead |
| Scaling | Mostly vertical | Native horizontal sharding |
| Query language | SQL | MongoDB Query Language and aggregation pipeline |
| Best for | Structured, highly relational data | Hierarchical, evolving, or high-volume semi-structured data |

## When to Use and When Not to Use MongoDB

**Good fit:**

- Content management, catalogs, and user profiles with varying attributes.
- Event logging, IoT, and time-series data.
- Applications whose schema changes rapidly.
- Data naturally shaped like documents or JSON (APIs, mobile backends).
- Workloads needing horizontal scale.

**Poor fit:**

- Highly relational data with many-to-many relationships and complex multi-table joins.
- Financial or accounting systems needing strict multi-table transactional guarantees everywhere.
- Data that is already tabular and well structured, where a relational database is simpler.

## Common MongoDB Mistakes

```text
1. Running without authentication or exposing the port to the internet.
2. Creating no indexes and relying on collection scans.
3. Letting arrays grow without bound.
4. Using skip() for deep pagination (use range queries on an indexed field instead).
5. Picking a poor shard key (for example, a monotonically increasing value).
6. Storing money as double instead of Decimal128 or integer cents.
7. Using w:1 for critical data and then losing writes on failover.
8. Modeling data exactly like relational tables and using $lookup everywhere.
```

**Remember:** In MongoDB, design the schema around **how the application reads and writes data**, not around how the data looks in isolation.

---

# Redis in Depth

## What is Redis?

Redis (**RE**mote **DI**ctionary **S**erver) is an open-source, **in-memory data structure store**. It is most often used as a **cache**, but it is also used as a database, message broker, and streaming engine. Unlike a plain key-value store that only holds strings, Redis values can be rich data structures such as lists, sets, sorted sets, hashes, and streams.

Key characteristics:

- **In-memory**: Data lives in RAM, giving sub-millisecond response times.
- **Key-value model**: Every item is accessed by a unique key.
- **Rich data structures**: Strings, Lists, Sets, Sorted Sets, Hashes, Bitmaps, HyperLogLogs, Geospatial indexes, Streams.
- **Optional persistence**: Data can be saved to disk (RDB snapshots, AOF log) so it survives restarts.
- **Replication and clustering**: Replicas, Sentinel for failover, and Redis Cluster for sharding.
- **Atomic operations**: Every single command is atomic.
- **Pub/Sub, transactions, and Lua scripting** built in.

> **Licensing note:** Redis changed its license in 2024, and community forks such as **Valkey** appeared. Commands and concepts in this chapter apply to Redis and Valkey alike, but check the current license terms if you plan to redistribute or host it as a service.

## Why is Redis So Fast?

| Reason | Explanation |
|---|---|
| Data in RAM | Memory access is orders of magnitude faster than disk |
| Efficient data structures | Purpose-built encodings keep memory use low and operations quick |
| Single-threaded command execution | No locks or context switching between commands, so each command runs atomically |
| I/O multiplexing | One thread handles thousands of connections using an event loop (`epoll`, `kqueue`) |
| Simple protocol (RESP) | Lightweight text-based protocol that is cheap to parse |
| Optional I/O threads | Newer versions can use extra threads for network reads and writes, while commands still run on one thread |

**Consequence:** A single slow command (for example, `KEYS *` on millions of keys) blocks every other client. Avoid O(N) commands on large datasets in production.

## Redis vs Traditional Databases

| Aspect | Relational Database | Redis |
|---|---|---|
| Primary storage | Disk | Memory |
| Data model | Tables, rows | Key → data structure |
| Query language | SQL | Command-based API |
| Relationships / joins | Yes | No |
| Typical latency | Milliseconds | Microseconds to sub-millisecond |
| Dataset size limit | Disk size | RAM size (per node) |
| Main role | System of record | Cache, fast store, messaging |

## Installing and Connecting

```bash
# Ubuntu / Debian
sudo apt install redis-server
sudo systemctl enable --now redis-server

# Docker
docker run --name redis-dev -p 6379:6379 -d redis:7

# Connect with the command-line client
redis-cli
redis-cli -h hostname -p 6379 -a yourpassword
```

```text
127.0.0.1:6379> PING
PONG
127.0.0.1:6379> SET greeting "Hello"
OK
127.0.0.1:6379> GET greeting
"Hello"
```

- Default port: **6379**.
- A standalone instance has **16 numbered logical databases** (0–15); switch with `SELECT 1`. Redis Cluster supports only database 0, so most modern designs use key prefixes instead of numbered databases.

## Keys and Key Design

A key is a binary-safe string (up to 512 MB, but keep keys short and readable). Use a consistent naming convention with colons as separators:

```text
user:1001:profile
user:1001:sessions
order:2026:5001:items
cache:product:42
rate:login:192.168.1.5
```

### Key Management Commands

| Command | Purpose |
|---|---|
| `EXISTS key` | Check whether a key exists |
| `DEL key` | Delete a key (blocking for large values) |
| `UNLINK key` | Delete a key asynchronously (preferred for big keys) |
| `TYPE key` | Show the data type of the value |
| `RENAME old new` | Rename a key |
| `EXPIRE key seconds` | Set a time-to-live |
| `PEXPIRE key ms` | Set a TTL in milliseconds |
| `TTL key` / `PTTL key` | Remaining life (`-1` = no expiry, `-2` = key does not exist) |
| `PERSIST key` | Remove the expiry |
| `SCAN cursor MATCH pattern COUNT n` | Iterate keys safely in small steps |
| `KEYS pattern` | List keys matching a pattern (**never use in production**, it blocks the server) |
| `DBSIZE` | Number of keys in the current database |
| `FLUSHDB` / `FLUSHALL` | Delete all keys in one database / in all databases (dangerous) |

> Use `SCAN`, never `KEYS`, on production systems.

## Data Types and Their Commands

### 1. Strings

The simplest type. A string can hold text, numbers, serialized JSON, or binary data (up to 512 MB).

```text
SET name "Rahul"
GET name
SET counter 10
INCR counter              → 11 (atomic increment)
INCRBY counter 5          → 16
DECR counter              → 15
APPEND name " Das"        → "Rahul Das"
STRLEN name
MSET a 1 b 2 c 3          (set many)
MGET a b c                (get many)
SET session:42 "token" EX 3600        (set with 1-hour expiry)
SET lock:job1 "x" NX                  (set only if the key does Not eXist)
SET key value XX                      (set only if the key already eXists)
GETSET / GETDEL / GETEX               (read and modify in one atomic step)
```

Uses: caching HTML/JSON, counters, flags, session tokens, distributed locks.

### 2. Lists

An ordered collection of strings, implemented as a linked list of compact nodes. Fast push and pop at both ends.

```text
LPUSH tasks "task1"       (insert at the head)
RPUSH tasks "task2"       (insert at the tail)
LRANGE tasks 0 -1         (all elements; -1 means the last)
LPOP tasks
RPOP tasks
LLEN tasks
LINDEX tasks 0
LTRIM tasks 0 99          (keep only the first 100 items)
BLPOP queue 30            (blocking pop, waits up to 30 seconds)
```

Uses: simple queues (`RPUSH` + `BLPOP`), activity feeds, "latest N items" (`LPUSH` + `LTRIM`).

### 3. Sets

An unordered collection of **unique** strings.

```text
SADD tags "redis" "database" "cache"
SMEMBERS tags
SISMEMBER tags "redis"    → 1
SREM tags "cache"
SCARD tags                (number of members)
SINTER set1 set2          (intersection)
SUNION set1 set2          (union)
SDIFF set1 set2           (difference)
SRANDMEMBER tags 2
SPOP tags
```

Uses: unique visitors, tagging, mutual friends (`SINTER`), removing duplicates.

### 4. Sorted Sets (ZSets)

A set of unique members where each member has a numeric **score**. Members are kept sorted by score.

```text
ZADD leaderboard 1500 "alice" 1200 "bob" 1800 "carol"
ZRANGE leaderboard 0 -1 WITHSCORES          (lowest to highest)
ZRANGE leaderboard 0 2 REV WITHSCORES       (top 3, highest first)
ZINCRBY leaderboard 100 "bob"               (add to a score)
ZRANK leaderboard "bob"                     (position, lowest first)
ZREVRANK leaderboard "bob"                  (position, highest first)
ZSCORE leaderboard "alice"
ZRANGEBYSCORE leaderboard 1000 1600         (range by score)
ZREM leaderboard "bob"
ZCARD leaderboard
```

Uses: leaderboards, priority queues, rate-limit sliding windows, time-ordered indexes (score = timestamp). Operations are O(log N).

### 5. Hashes

A map of field → value inside one key, like a small object or a row.

```text
HSET user:1001 name "Asha" city "Kolkata" age 28
HGET user:1001 name
HGETALL user:1001
HMGET user:1001 name city
HINCRBY user:1001 age 1
HDEL user:1001 city
HEXISTS user:1001 name
HLEN user:1001
```

Uses: user profiles, product details, session data. A hash is more memory-efficient than many separate string keys, and one field can be updated without rewriting the whole object.

### 6. Bitmaps

Not a separate type; bit-level operations on strings.

```text
SETBIT login:2026-10-26 1001 1      (user 1001 logged in today)
GETBIT login:2026-10-26 1001
BITCOUNT login:2026-10-26           (how many users logged in)
BITOP AND active:both login:day1 login:day2
```

Uses: daily active users, feature flags, attendance. One million users fit in about 125 KB.

### 7. HyperLogLog

A probabilistic structure for counting **approximate unique** items using at most about 12 KB, with a standard error of roughly 0.81%.

```text
PFADD visitors "user1" "user2" "user3"
PFCOUNT visitors
PFMERGE all_visitors visitors_day1 visitors_day2
```

Uses: unique visitor counts at huge scale where an exact count is unnecessary.

### 8. Geospatial Indexes

Store longitude/latitude pairs (internally a sorted set).

```text
GEOADD stores 88.3639 22.5726 "kolkata-hq" 88.4339 22.5958 "saltlake"
GEODIST stores "kolkata-hq" "saltlake" km
GEOSEARCH stores FROMLONLAT 88.36 22.57 BYRADIUS 10 km ASC
```

Uses: "nearby" searches, delivery and ride matching.

### 9. Streams

An append-only log of entries, each with an auto-generated ID and field-value pairs. Supports **consumer groups** for reliable processing.

```text
XADD orders * customer 101 total 1500          (append; * auto-generates the ID)
XLEN orders
XRANGE orders - +                              (read all)
XREAD COUNT 10 STREAMS orders 0                (read from the start)

XGROUP CREATE orders workers $ MKSTREAM        (create a consumer group)
XREADGROUP GROUP workers worker1 COUNT 5 STREAMS orders >
XACK orders workers 1700000000000-0            (acknowledge processing)
XPENDING orders workers                        (see unacknowledged entries)
```

Uses: event sourcing, durable message queues, activity logs, work distribution across consumers.

### 10. Extended Types (Modules / Redis Stack)

| Type | Purpose |
|---|---|
| JSON | Store and query JSON documents natively |
| Search | Secondary indexes, full-text and vector search |
| TimeSeries | Time-series data with downsampling |
| Bloom / Cuckoo filters | Probabilistic "has this been seen?" checks |

Availability depends on the Redis version or distribution (for example, Redis Stack or recent Redis releases).

### Choosing the Right Data Type

| Need | Use |
|---|---|
| Cache a value or counter | String |
| Queue or "latest N" list | List (or Stream for reliability) |
| Unique items, set operations | Set |
| Ranking or ordered by score | Sorted Set |
| Object with several fields | Hash |
| Boolean flag per ID at large scale | Bitmap |
| Approximate unique count | HyperLogLog |
| Nearby-location search | Geo |
| Durable log with consumer groups | Stream |

## Expiration and Eviction

### Expiration (TTL)

Keys can be given a lifetime and are deleted automatically afterwards.

```text
SET otp:9876 "123456" EX 300      (expires in 5 minutes)
TTL otp:9876
```

Redis removes expired keys in two ways: **lazily** (when a client accesses an expired key) and **actively** (a background process periodically samples keys with TTLs).

### Eviction Policies

When memory reaches the `maxmemory` limit, Redis uses the `maxmemory-policy` setting.

| Policy | Behaviour |
|---|---|
| `noeviction` (default) | Reject writes that need more memory; reads still work |
| `allkeys-lru` | Evict the least recently used key among all keys (**common for caches**) |
| `allkeys-lfu` | Evict the least frequently used key among all keys |
| `allkeys-random` | Evict random keys |
| `volatile-lru` | LRU among keys that have a TTL |
| `volatile-lfu` | LFU among keys that have a TTL |
| `volatile-random` | Random among keys that have a TTL |
| `volatile-ttl` | Evict keys with the shortest remaining TTL |

```text
# redis.conf
maxmemory 2gb
maxmemory-policy allkeys-lru
```

> Always set `maxmemory` for a cache. Without it, Redis can use all available RAM and the operating system may kill it.

## Persistence

Because Redis lives in memory, persistence is what lets data survive a restart.

### RDB (Snapshotting)

Periodically writes a compact point-in-time snapshot (`dump.rdb`) to disk by forking the process.

```text
# redis.conf: snapshot if at least 1 key changed in 900 s, or 10 keys in 300 s, or 10000 keys in 60 s
save 900 1
save 300 10
save 60 10000
```

Manual commands: `SAVE` (blocks the server) and `BGSAVE` (background; preferred).

### AOF (Append Only File)

Logs every write command; on restart, Redis replays the log to rebuild the dataset.

```text
appendonly yes
appendfsync everysec      # always | everysec | no
```

| `appendfsync` | Durability | Speed |
|---|---|---|
| `always` | Safest; fsync after every write | Slowest |
| `everysec` (default) | Can lose about 1 second of writes | Good balance |
| `no` | OS decides when to flush | Fastest, least safe |

The AOF grows over time, so Redis periodically **rewrites** it into a smaller equivalent file.

### RDB vs AOF vs Both

| Aspect | RDB | AOF |
|---|---|---|
| Format | Compact binary snapshot | Log of commands |
| Data loss on crash | Everything since the last snapshot | About 1 second (with `everysec`) or less |
| Restart speed | Fast | Slower (replays commands) |
| File size | Small | Larger |
| Best for | Backups, fast restarts | Durability |

**Recommendation:** Use **both** where data matters. Use **neither** if Redis is purely a rebuildable cache. Persistence on disk does not replace proper backups; copy RDB files to separate storage.

## Transactions

Redis transactions group commands so they run **one after another with no other client's commands in between**.

```text
MULTI                    (start queuing)
DECRBY account:A 500
INCRBY account:B 500
EXEC                     (run all queued commands)
```

- `DISCARD` cancels the queued commands.
- **There is no rollback.** If one command fails at run time, the others still execute. A syntax error while queuing aborts the whole transaction.
- **Optimistic locking with `WATCH`:** If a watched key changes before `EXEC`, the transaction is cancelled and the client retries.

```text
WATCH balance:A
GET balance:A
MULTI
SET balance:A 450
EXEC                     → nil if balance:A was modified by someone else after WATCH
```

For complex atomic logic, use **Lua scripts** (`EVAL`) or **Redis Functions**, which execute atomically on the server.

```text
EVAL "return redis.call('INCR', KEYS[1])" 1 mycounter
```

## Pipelining

Sending commands one at a time wastes time on network round trips. **Pipelining** sends many commands at once and reads all replies together, which can improve throughput many times over. A pipeline does **not** guarantee atomicity (use `MULTI/EXEC` for that).

```python
pipe = r.pipeline()
for i in range(1000):
    pipe.set(f"key:{i}", i)
pipe.execute()
```

## Pub/Sub

A lightweight messaging pattern where publishers send messages to **channels** and subscribers receive them.

```text
# Client A
SUBSCRIBE news
# Client B
PUBLISH news "Server will restart at 10 PM"
```

- Delivery is **fire-and-forget (at-most-once)**: if a subscriber is offline, it misses the message, and nothing is stored.
- Pattern subscriptions are available with `PSUBSCRIBE news.*`.
- For reliable messaging with history and acknowledgements, use **Streams** instead.

## Replication

Redis uses **asynchronous primary-replica replication**.

```text
        Primary (reads + writes)
         /           \
   Replica 1       Replica 2     (read-only copies)
```

```text
# On the replica
REPLICAOF primary-host 6379
```

- A new replica receives a full copy (RDB), then streams ongoing changes.
- Replicas can serve read traffic, but may return slightly **stale** data due to asynchronous replication.
- `WAIT numreplicas timeout` can block until a number of replicas acknowledge a write, but this does not make Redis strongly consistent.

## High Availability: Redis Sentinel

**Sentinel** is a separate set of processes that monitors a primary and its replicas.

| Function | Description |
|---|---|
| Monitoring | Checks whether primary and replicas are alive |
| Automatic failover | Promotes a replica to primary if the primary fails (needs a quorum of Sentinels) |
| Configuration provider | Tells clients the address of the current primary |
| Notification | Alerts administrators |

Run **at least three** Sentinels (an odd number) on separate machines so a majority can always agree.

## Scaling: Redis Cluster

Redis Cluster shards data across multiple primaries, each with optional replicas.

- The key space is divided into **16,384 hash slots**.
- A key's slot is `CRC16(key) mod 16384`, and each primary owns a range of slots.
- Clients are redirected (`MOVED`) to the node owning a key's slot.
- Each node also uses a cluster bus port (data port + 10000, for example 16379).
- Multi-key commands only work if all keys are in the **same slot**. **Hash tags** force this: only the part inside `{}` is hashed.

```text
SET {user:1001}:profile "..."
SET {user:1001}:cart "..."        # Both keys land in the same slot
```

| Setup | Purpose |
|---|---|
| Single instance | Simple, development, small caches |
| Primary + replicas | Read scaling and data redundancy |
| Primary + replicas + Sentinel | Automatic failover for one dataset that fits on one node |
| Redis Cluster | Data larger than one node's RAM, plus failover |

## Caching with Redis

Caching is the most common use of Redis.

### Caching Strategies

| Strategy | How it works | Trade-off |
|---|---|---|
| **Cache-Aside (Lazy Loading)** | App checks the cache; on a miss, reads the database and writes to the cache | Most common; first request is slow; data can go stale |
| Read-Through | The cache layer itself loads data from the database on a miss | Simpler app code; needs cache-layer support |
| Write-Through | Writes go to the cache and the database together | Cache is consistent; writes are slower |
| Write-Behind (Write-Back) | Writes go to the cache first and are saved to the database later | Fast writes; risk of data loss if the cache fails |

Cache-aside example:

```python
def get_product(product_id):
    key = f"cache:product:{product_id}"
    cached = r.get(key)
    if cached:
        return json.loads(cached)               # Cache hit

    product = db.query_product(product_id)      # Cache miss: go to the database
    r.set(key, json.dumps(product), ex=600)     # Cache it for 10 minutes
    return product

def update_product(product_id, data):
    db.update_product(product_id, data)
    r.delete(f"cache:product:{product_id}")     # Invalidate so the next read refreshes it
```

### Classic Caching Problems

| Problem | Description | Typical Fix |
|---|---|---|
| Cache penetration | Requests for keys that do not exist anywhere bypass the cache and hit the database every time | Cache the "not found" result briefly; use a Bloom filter; validate input |
| Cache breakdown (hot key expiry) | One very popular key expires and many requests rush to the database at once | Mutex lock around rebuild; never expire the hottest keys; refresh in the background |
| Cache avalanche | Many keys expire at the same moment, or the cache goes down, flooding the database | Add random jitter to TTLs; use replicas/cluster; rate-limit and degrade gracefully |
| Stale data | Cache and database disagree after an update | Invalidate on write; use short TTLs |
| Cache stampede | Many clients rebuild the same missing value simultaneously | Request coalescing or locking |

## Common Use Cases and Patterns

### Session Store

```text
HSET session:abc123 user_id 1001 role "admin"
EXPIRE session:abc123 1800
```

### Rate Limiter (Fixed Window)

```text
INCR rate:user:1001:2026-10-26T10:15        (count requests in this minute)
EXPIRE rate:user:1001:2026-10-26T10:15 60   (set only when the counter is first created)
# If the count exceeds the limit, reject the request
```

### Leaderboard

```text
ZADD game:leaderboard 2500 "player42"
ZINCRBY game:leaderboard 50 "player42"
ZRANGE game:leaderboard 0 9 REV WITHSCORES      (top 10)
ZREVRANK game:leaderboard "player42"            (player's rank)
```

### Distributed Lock

```text
SET lock:invoice:77 <unique-token> NX PX 30000
```

- `NX` makes only one client succeed; `PX 30000` auto-releases after 30 seconds if the holder crashes.
- Release the lock only if the stored token matches yours, using a Lua script so check-and-delete is atomic:

```text
EVAL "if redis.call('GET', KEYS[1]) == ARGV[1] then return redis.call('DEL', KEYS[1]) else return 0 end" 1 lock:invoice:77 <unique-token>
```

- A single-instance lock is not fully safe across failovers. Distributed algorithms such as Redlock exist but are debated; for correctness-critical work, add fencing tokens or use a system built for coordination.

### Job Queue

```text
RPUSH jobs "send-email:55"       (producer)
BLPOP jobs 30                    (worker waits for a job)
```

For reliable queues with acknowledgement and retries, use **Streams** with consumer groups.

### Real-Time Counters and Analytics

```text
INCR page:views:home
PFADD unique:visitors:2026-10-26 "user1"
SETBIT active:2026-10-26 1001 1
```

## Security

| Practice | Description |
|---|---|
| Never expose Redis to the internet | Bind to private interfaces, use firewalls and private networks |
| Set a strong password / use ACLs | `requirepass`, or per-user rules with ACLs (Redis 6+) |
| `protected-mode` | Keep enabled so unauthenticated remote access is refused by default |
| TLS | Encrypt client and replication traffic |
| Disable or restrict dangerous commands | `FLUSHALL`, `CONFIG`, `KEYS`, `DEBUG` via ACLs |
| Run as an unprivileged user | Never as root |

```text
ACL SETUSER appuser on >StrongPassword ~cache:* +get +set +del +expire
```

This creates a user that can only run `GET`, `SET`, `DEL`, and `EXPIRE` on keys starting with `cache:`.

> Default Redis has historically had no password. An internet-exposed, unauthenticated Redis is easily compromised and has been used to take over servers.

## Monitoring and Troubleshooting

| Command / Tool | Purpose |
|---|---|
| `INFO` (and `INFO memory`, `INFO stats`, `INFO replication`) | Server statistics |
| `SLOWLOG GET 10` | Show recent slow commands |
| `MONITOR` | Stream every command in real time (heavy; avoid on busy production servers) |
| `MEMORY USAGE key` | Memory used by one key |
| `redis-cli --bigkeys` | Find the largest keys by type |
| `redis-cli --latency` | Measure latency |
| `redis-benchmark` | Load-test a server |
| `CLIENT LIST` | See connected clients |
| `LATENCY DOCTOR` | Diagnose latency spikes |

Key metrics to watch:

```text
used_memory vs maxmemory      keyspace_hits / keyspace_misses (hit ratio)
evicted_keys                  expired_keys
connected_clients             instantaneous_ops_per_sec
rejected_connections          master_repl_offset / replication lag
latest_fork_usec              rdb_last_bgsave_status
```

A healthy cache usually has a **hit ratio** above roughly 90%. A rising `evicted_keys` count means memory is too small for the working set.

## Using Redis from Application Code

```python
import redis

r = redis.Redis(host="localhost", port=6379, decode_responses=True)

r.set("greeting", "Hello", ex=60)
print(r.get("greeting"))

r.hset("user:1001", mapping={"name": "Asha", "city": "Kolkata"})
print(r.hgetall("user:1001"))

r.zadd("leaderboard", {"alice": 1500, "bob": 1200})
print(r.zrange("leaderboard", 0, 2, desc=True, withscores=True))
```

Use a **connection pool** (most clients do this by default) rather than opening a new connection for every request.

## Redis vs Memcached

| Aspect | Redis | Memcached |
|---|---|---|
| Data types | Rich structures | Strings only |
| Persistence | Yes (RDB/AOF) | No |
| Replication / clustering | Built in | Client-side sharding only |
| Pub/Sub, Streams, scripting | Yes | No |
| Threading | Single-threaded commands (I/O threads optional) | Multi-threaded |
| Best for | Cache plus data-structure features | Simple, large, multi-core caches |

## Redis vs MongoDB

| Aspect | Redis | MongoDB |
|---|---|---|
| Model | Key → data structure | Documents in collections |
| Storage | In memory (disk optional for persistence) | On disk (cache in memory) |
| Query capability | Key-based access and structure commands | Rich queries, aggregation, joins via `$lookup` |
| Speed | Sub-millisecond | Milliseconds |
| Dataset size | Limited by RAM | Limited by disk |
| Typical role | Cache, sessions, queues, leaderboards | Primary operational database |
| Together | Redis often **sits in front of** MongoDB or SQL as a cache | |

## When to Use and When Not to Use Redis

**Good fit:**

- Caching database results, pages, and API responses.
- Session storage and temporary tokens (OTPs).
- Leaderboards, counters, rate limiting.
- Real-time messaging, queues, and lightweight streams.
- Anything requiring very low latency over a modest dataset.

**Poor fit:**

- Primary storage for large datasets that exceed available RAM.
- Complex relational queries, joins, or ad-hoc reporting.
- Data that must never be lost, unless persistence and replication are carefully configured and the trade-offs accepted.

## Common Redis Mistakes

```text
1. Running KEYS * (or other O(N) commands) on a production server.
2. Not setting maxmemory and an eviction policy for a cache.
3. Exposing Redis to the internet without authentication.
4. Storing huge values or collections under a single key (big keys block the server).
5. Giving every key the same TTL, causing a cache avalanche.
6. Treating Redis as the only copy of important data without persistence or backups.
7. Expecting MULTI/EXEC to roll back on error.
8. Using Pub/Sub when message delivery must be guaranteed.
9. Forgetting that replication is asynchronous, so a failover can lose recent writes.
```

**Remember:** Redis is fastest when used for the right job: **small, hot, frequently accessed data**, with the database remaining the source of truth.

---

# Integrity Constraints

## Entity Integrity
Ensures that the primary key of a table cannot contain NULL values, guaranteeing every row is uniquely identifiable.

## Referential Integrity
Ensures that a foreign key value must either match an existing primary key value in the referenced table or be NULL.

## Domain Constraints
Restricts the values that can be stored in a column to a specific data type, range, or set of allowed values.

## Check Constraints
User-defined rules that restrict the values that can be entered into a column beyond basic data type restrictions.

---

# OLTP vs OLAP

| Aspect | OLTP | OLAP |
|---|---|---|
| Purpose | Day-to-day transactions | Analysis and reporting |
| Data | Current, detailed | Historical, aggregated |
| Operations | INSERT/UPDATE/DELETE heavy | Complex SELECT/aggregation heavy |
| Example | Order processing system | Data warehouse |

---

# Installing and Setting Up Database Software

## Choosing a Database System

| Database | Type | Typical Use |
|---|---|---|
| MySQL / MariaDB | Relational, open source | Web applications, CMS, startups |
| PostgreSQL | Relational, open source | Complex queries, analytics, enterprise apps |
| SQLite | Relational, embedded (single file) | Mobile apps, learning, prototypes, local tools |
| Microsoft SQL Server | Relational, commercial | Windows/.NET enterprise environments |
| Oracle Database | Relational, commercial | Large enterprises, banking, ERP |
| MongoDB | NoSQL document | Flexible schemas, large unstructured data |
| Redis | NoSQL key-value | Caching, sessions, queues |

> For learning SQL, SQLite (zero setup), MySQL, and PostgreSQL are the easiest starting points. Core SQL (`SELECT`, `JOIN`, `GROUP BY`) works across all of them, with small syntax differences.

## Installing on Linux

### MySQL

```bash
sudo apt update
sudo apt install mysql-server
sudo systemctl enable --now mysql
sudo mysql_secure_installation        # Set root password, remove anonymous users, disable remote root login
sudo mysql                            # Connect as root through the local socket
```

### PostgreSQL

```bash
sudo apt install postgresql postgresql-contrib
sudo systemctl enable --now postgresql
sudo -u postgres psql                 # Open psql as the postgres superuser
```

### SQLite

```bash
sudo apt install sqlite3
sqlite3 practice.db                   # Creates the file if it does not exist
```

Useful SQLite shell commands (start with a dot, not SQL):

```text
.tables            # List tables
.schema Employee   # Show CREATE statement for a table
.headers on        # Show column names in output
.mode column       # Align output in columns
.read script.sql   # Run a SQL script file
.quit              # Exit
```

## Installing on Windows and macOS

| Platform | Common Approach |
|---|---|
| Windows | Official installer (MySQL Installer, PostgreSQL installer, SQL Server Express) |
| macOS | `brew install mysql`, `brew install postgresql`, or Postgres.app |
| Any OS | Docker container (see below) |

## Running a Database with Docker

Docker provides a disposable database without installing anything on the host system.

```bash
# MySQL
docker run --name mysql-dev -e MYSQL_ROOT_PASSWORD=ChangeMe123 -p 3306:3306 -d mysql:8

# PostgreSQL
docker run --name pg-dev -e POSTGRES_PASSWORD=ChangeMe123 -p 5432:5432 -d postgres:16

# Connect
docker exec -it mysql-dev mysql -u root -p
docker exec -it pg-dev psql -U postgres

# Stop and remove
docker stop mysql-dev && docker rm mysql-dev
```

> Without a volume (`-v`), data is lost when the container is removed. Use a volume for anything worth keeping.

## Default Ports

| Database | Default Port |
|---|---|
| MySQL / MariaDB | 3306 |
| PostgreSQL | 5432 |
| SQL Server | 1433 |
| Oracle | 1521 |
| MongoDB | 27017 |
| Redis | 6379 |

## Connecting to a Database

### Command-Line Clients

```bash
mysql -u username -p -h hostname -P 3306 database_name
psql -U username -h hostname -p 5432 -d database_name
sqlite3 file.db
```

### Graphical Clients

| Tool | Works With |
|---|---|
| DBeaver | Almost every database (free) |
| MySQL Workbench | MySQL / MariaDB |
| pgAdmin | PostgreSQL |
| DataGrip | Many databases (paid) |
| SQL Server Management Studio (SSMS) | SQL Server |
| DB Browser for SQLite | SQLite |

### Connection Details

Every client needs the same five pieces of information:

```text
Host       → where the server runs (localhost, or an IP/DNS name)
Port       → listening port (3306, 5432, ...)
Database   → which database to open
Username   → account to log in with
Password   → account secret
```

> Connection errors almost always come from one of these five: wrong host, wrong port, server not running, firewall blocking the port, or wrong credentials.

## Loading Sample Data

```bash
mysql -u root -p database_name < dump.sql           # Load a SQL dump into MySQL
psql -U postgres -d database_name -f dump.sql       # Load into PostgreSQL
sqlite3 practice.db < dump.sql                      # Load into SQLite
```

Well-known practice databases: Sakila and World (MySQL), Pagila and DVD Rental (PostgreSQL), Chinook (multi-database), Northwind (multi-database).

---

# Writing SQL Code

## Running SQL

| Method | Description |
|---|---|
| Interactive shell | Type statements in `mysql`, `psql`, or `sqlite3` |
| Script file | Save statements in a `.sql` file and run it in one go |
| GUI editor | Write and run statements in DBeaver, Workbench, pgAdmin, and so on |
| Application code | Python, Java, Node.js, and similar send SQL through a database driver |

```bash
mysql -u root -p mydb < setup.sql
psql -U postgres -d mydb -f setup.sql
```

## Statement Basics

```sql
-- Single-line comment
/* Multi-line
   comment */

SELECT name, salary     -- statements end with a semicolon
FROM Employee
WHERE salary > 40000;
```

- Keywords are case-insensitive (`SELECT` = `select`), but uppercase keywords improve readability.
- Table and column names may be case-sensitive depending on the database and operating system.
- String literals use single quotes (`'Kolkata'`), not double quotes.
- One statement ends at a semicolon, so a missing `;` is a common cause of "nothing happens" in a shell.

## Formatting Conventions

```sql
-- Hard to read
select e.name,d.dept_name,avg(e.salary) from employee e join department d on e.dept_id=d.dept_id where e.salary>30000 group by e.name,d.dept_name having avg(e.salary)>40000 order by 3 desc;

-- Readable
SELECT
    d.dept_name,
    AVG(e.salary) AS avg_salary
FROM Employee e
INNER JOIN Department d
    ON e.dept_id = d.dept_id
WHERE e.salary >= 30000
GROUP BY d.dept_name
HAVING AVG(e.salary) > 40000
ORDER BY avg_salary DESC;
```

| Convention | Guideline |
|---|---|
| Keywords | UPPERCASE |
| Identifiers | `snake_case` (`order_items`, `created_at`) |
| Clauses | One clause per line, indented columns |
| Aliases | Short, meaningful, with `AS` for column aliases |
| Table names | Singular or plural, but consistent across the whole schema |
| Columns | List them explicitly; avoid `SELECT *` in application code |

## Habits for Safe Queries

```sql
-- Preview the rows first, then change them
SELECT * FROM Employee WHERE dept_id = 3;
UPDATE Employee SET salary = salary * 1.10 WHERE dept_id = 3;

-- Wrap risky changes in a transaction
BEGIN;
DELETE FROM Orders WHERE order_date < '2020-01-01';
SELECT COUNT(*) FROM Orders;      -- Verify the result looks right
ROLLBACK;                         -- or COMMIT; once you are sure
```

| Habit | Reason |
|---|---|
| Run a `SELECT` with the same `WHERE` before `UPDATE`/`DELETE` | Confirms exactly which rows will be affected |
| Check the affected row count after every write | A surprising number signals a wrong condition |
| Use transactions for multi-step changes | `ROLLBACK` can undo mistakes (where the database supports it) |
| Use `LIMIT` while exploring large tables | Avoids long-running or huge result sets |
| Never write an `UPDATE`/`DELETE` without `WHERE` | It changes every row |
| Back up before schema changes | `DROP` and `ALTER` can be irreversible |

## Parameterized Queries

Applications must never build SQL by joining user input into a string.

```python
# Vulnerable to SQL injection
query = "SELECT * FROM Users WHERE name = '" + user_input + "'"

# Safe: the driver sends the value separately from the SQL text
cursor.execute("SELECT * FROM Users WHERE name = %s", (user_input,))
```

If `user_input` is `' OR '1'='1`, the first version returns every user. The second treats the whole input as a plain value.

> SQL injection is consistently among the most common database-related security flaws. Parameterized queries (prepared statements) are the standard defence.

## Common Table Expressions

A `WITH` clause names an intermediate result, which makes long queries easier to read and test.

```sql
WITH dept_avg AS (
    SELECT dept_id, AVG(salary) AS avg_salary
    FROM Employee
    GROUP BY dept_id
)
SELECT e.name, e.salary, d.avg_salary
FROM Employee e
JOIN dept_avg d ON e.dept_id = d.dept_id
WHERE e.salary > d.avg_salary;
```

## Project Organization

```text
db/
├── schema/          # CREATE TABLE definitions
├── migrations/      # Ordered, versioned change scripts
├── seed/            # Sample or reference data
└── queries/         # Reusable reports and queries
```

Keep every `.sql` file in version control (Git) so that schema changes are reviewed and tracked like application code.

---

# Debugging SQL

## Reading Error Messages

An error message usually gives three clues: the error type or code, the position (line/near text), and the object name. Read the text immediately before the quoted "near" fragment, since the mistake is often just before it.

## Common Errors and Causes

| Error | Typical Cause | Fix |
|---|---|---|
| Syntax error near ... | Missing comma, quote, parenthesis, or keyword typo | Check the text just before the reported position |
| Unknown column / column does not exist | Typo, wrong table, or alias used before it exists | Verify names with `DESCRIBE table` / `\d table` |
| Table doesn't exist | Wrong database selected or name misspelled | Check the active database (`USE db;`), list tables |
| Ambiguous column name | Same column name in two joined tables | Prefix with table alias (`e.dept_id`) |
| Column must appear in GROUP BY | Selecting non-aggregated columns not in `GROUP BY` | Add the column to `GROUP BY` or aggregate it |
| Aggregate function not allowed in WHERE | `WHERE COUNT(*) > 3` | Use `HAVING` |
| Duplicate entry / unique violation | Inserting a value that already exists in a unique/primary key column | Check existing rows, use upsert or correct the data |
| Foreign key constraint fails | Parent row missing, or child rows still exist on delete | Insert parent first / delete children first |
| Cannot be null / NOT NULL violation | Required value missing from `INSERT` | Supply a value or define a default |
| Data too long for column | Value exceeds `VARCHAR(n)` | Shorten the value or widen the column |
| Deadlock found | Two transactions waiting on each other's locks | Retry the transaction; keep lock order consistent |
| Access denied | Wrong credentials or missing privilege | Check user, host, and `GRANT` rights |

## Silent Logic Bugs

Queries that run without errors but return wrong results are harder to find.

| Symptom | Likely Cause |
|---|---|
| Row count suddenly multiplies | Join on a non-unique column, or missing join condition (accidental cross join) |
| Rows missing from a `LEFT JOIN` result | Filter on the right table placed in `WHERE` instead of `ON` |
| `= NULL` returns nothing | Must use `IS NULL` |
| `NOT IN` returns nothing | The subquery contains a `NULL`; use `NOT EXISTS` |
| Averages look wrong | `NULL` values are ignored by `AVG`; integer division truncates |
| Sorting looks wrong | Numbers stored as text sort alphabetically (`'10'` < `'9'`) |
| Dates compare incorrectly | Date stored as text, or timezone mismatch |

## Debugging Strategy

```text
1. Reproduce the problem with the smallest possible query.
2. Run each subquery or CTE on its own and check its output.
3. Add joins and filters one at a time, checking row counts at each step.
4. Compare COUNT(*) before and after each join.
5. Inspect suspicious values directly (SELECT DISTINCT, MIN, MAX, COUNT of NULLs).
6. Check data types (DESCRIBE / \d / PRAGMA table_info).
```

```sql
-- Quick data-quality checks
SELECT COUNT(*) FROM Employee;
SELECT COUNT(*) FROM Employee WHERE dept_id IS NULL;
SELECT dept_id, COUNT(*) FROM Employee GROUP BY dept_id;
SELECT DISTINCT status FROM Orders;

-- Find duplicates
SELECT email, COUNT(*)
FROM Users
GROUP BY email
HAVING COUNT(*) > 1;
```

## Query Execution Plans

`EXPLAIN` shows how the database intends to run a query, which is the first tool for slow-query debugging.

```sql
EXPLAIN SELECT * FROM Orders WHERE customer_id = 42;
EXPLAIN ANALYZE SELECT * FROM Orders WHERE customer_id = 42;   -- PostgreSQL / MySQL 8: actually runs it and reports real timings
```

| Plan Clue | Meaning |
|---|---|
| Full table scan / `type: ALL` / Seq Scan | Reads every row; usually a missing index on a large table |
| Index scan / `ref` / `range` | Uses an index (usually good) |
| Large estimated vs actual rows gap | Outdated statistics; run `ANALYZE` |
| Sort / temporary table on large data | Consider an index that matches `ORDER BY` / `GROUP BY` |

> `EXPLAIN ANALYZE` executes the statement. Never use it on an `UPDATE` or `DELETE` unless inside a transaction that will be rolled back.

## Logs and Monitoring Tools for Debugging

| Tool | Purpose |
|---|---|
| Slow query log (MySQL) / `log_min_duration_statement` (PostgreSQL) | Records queries exceeding a time threshold |
| `SHOW PROCESSLIST` / `pg_stat_activity` | Shows currently running queries and who runs them |
| Error log | Server startup problems, crashes, permission errors |
| `SHOW ENGINE INNODB STATUS` / `pg_locks` | Inspect lock waits and deadlocks |

---

# Database in Enterprise Production

## Environments

Production software never runs against a single database. Changes move through separate environments.

| Environment | Purpose | Data |
|---|---|---|
| Development | Developers write and test code | Small fake or sample data |
| Testing / QA | Automated and manual testing | Synthetic or anonymized data |
| Staging | Final rehearsal, mirrors production | Production-like (masked) |
| Production | Real users and real business data | Live, critical |

> Never test or experiment directly in production. Real customer data in lower environments must be masked or anonymized.

## Typical Architecture

```text
Users / Browsers / Mobile Apps
          ↓
   Load Balancer
          ↓
 Application Servers (many)
          ↓
   Connection Pool (PgBouncer, HikariCP, ProxySQL)
          ↓
   Primary Database  ── replication ──→  Read Replicas (reporting, read-heavy queries)
          ↓
   Backups / Archives (stored separately)
```

| Component | Role |
|---|---|
| Primary (master) | Accepts all writes |
| Replica (standby/slave) | Copies data from the primary; serves reads, takes over on failure |
| Connection pool | Reuses a limited number of database connections across many application requests |
| Backup storage | Separate location from the database server |

## How a Production Schema Looks

Real systems have dozens to thousands of tables, with consistent conventions. An e-commerce example:

```text
customers ──< orders ──< order_items >── products >── categories
                │
                └──< payments
```

```sql
CREATE TABLE customers (
    customer_id   BIGINT PRIMARY KEY AUTO_INCREMENT,
    email         VARCHAR(255) NOT NULL UNIQUE,
    full_name     VARCHAR(100) NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    is_active     BOOLEAN NOT NULL DEFAULT TRUE,
    created_at    TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at    TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);

CREATE TABLE orders (
    order_id     BIGINT PRIMARY KEY AUTO_INCREMENT,
    customer_id  BIGINT NOT NULL,
    status       VARCHAR(20) NOT NULL DEFAULT 'PENDING',
    total_amount DECIMAL(12,2) NOT NULL CHECK (total_amount >= 0),
    created_at   TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);

CREATE INDEX idx_orders_customer ON orders(customer_id);
CREATE INDEX idx_orders_status_created ON orders(status, created_at);
```

Common features seen in production tables:

| Feature | Example | Why |
|---|---|---|
| Surrogate primary key | `id BIGINT AUTO_INCREMENT` or UUID | Stable, never changes |
| Audit columns | `created_at`, `updated_at`, `created_by` | Track when and who changed data |
| Soft delete | `is_deleted` or `deleted_at` | Keep history instead of removing rows |
| Constraints | `NOT NULL`, `UNIQUE`, `CHECK`, `FOREIGN KEY` | Data integrity enforced by the database |
| Status columns | `status VARCHAR(20)` or enum/lookup table | Workflow states |
| Indexes on foreign keys and filters | `idx_orders_customer` | Fast joins and lookups |
| Money as `DECIMAL` | `DECIMAL(12,2)` | Never `FLOAT` for currency |
| Timestamps in UTC | `TIMESTAMP` stored as UTC | Avoids timezone confusion across regions |
| Hashed passwords | `password_hash` | Never store plaintext passwords |

## Schema Migrations

Production schemas are never changed by hand-typing statements. Each change is a versioned script applied in order by a migration tool (Flyway, Liquibase, Alembic, Django migrations, Prisma, and similar).

```text
migrations/
├── V001__create_customers.sql
├── V002__create_orders.sql
├── V003__add_phone_to_customers.sql
└── V004__index_orders_status.sql
```

```sql
-- V003__add_phone_to_customers.sql
ALTER TABLE customers ADD COLUMN phone VARCHAR(20);
```

| Principle | Description |
|---|---|
| Versioned and ordered | Every environment reaches the same schema state |
| Immutable | Never edit a migration that has already been applied; add a new one |
| Reviewed | Changes go through code review like application code |
| Backward compatible | The old application version must still work during rollout (add columns before using them, remove only after nothing uses them) |
| Reversible plan | Know how to roll back before applying |

## Users, Roles, and Least Privilege

Applications and people never connect as the superuser.

```sql
CREATE ROLE app_readwrite;
GRANT SELECT, INSERT, UPDATE, DELETE ON shop.* TO app_readwrite;

CREATE ROLE reporting_readonly;
GRANT SELECT ON shop.* TO reporting_readonly;
```

| Account Type | Privileges |
|---|---|
| Application | Only DML on its own schema; no `DROP`, no `GRANT` |
| Reporting / analyst | `SELECT` only, often on a replica |
| Migration tool | DDL rights, used only during deployment |
| DBA | Administrative rights, with individual accounts and auditing |

Credentials belong in a secrets manager or environment variables, never in source code or committed files.

## Backups and Recovery

| Practice | Description |
|---|---|
| Automated schedule | Full backups plus incrementals, run by a scheduler, not by hand |
| Point-in-time recovery | Transaction logs (binlog / WAL) allow restoring to an exact moment |
| Offsite copies | Backups stored away from the database server |
| Restore tests | A backup is only proven when a restore has been tested |
| Retention policy | Define how long each backup is kept |

```bash
mysqldump -u root -p --single-transaction shop > shop_backup.sql
pg_dump -U postgres -Fc shop > shop_backup.dump
```

Two figures define recovery goals:

| Term | Meaning |
|---|---|
| RPO (Recovery Point Objective) | Maximum acceptable data loss, measured in time |
| RTO (Recovery Time Objective) | Maximum acceptable time to restore service |

## High Availability and Scaling

| Technique | Purpose |
|---|---|
| Replication | Copies data to standby servers |
| Failover | Automatically promotes a replica if the primary fails |
| Read replicas | Spread read traffic across several servers |
| Connection pooling | Prevents running out of database connections |
| Partitioning | Splits a huge table by range or key (for example by month) |
| Sharding | Splits data across multiple database servers |
| Caching (Redis, Memcached) | Reduces repeated reads from the database |
| Archiving | Moves old data to cheaper storage |

## Monitoring

Metrics tracked continuously in production:

```text
Query latency (p95, p99)      Connections in use vs maximum
Slow queries                  Replication lag
CPU, memory, disk I/O         Disk space and growth rate
Lock waits and deadlocks      Cache hit ratio
Backup success/failure        Error rate
```

Common tools: Prometheus + Grafana, Datadog, New Relic, CloudWatch, pgAdmin / MySQL Enterprise Monitor, `pg_stat_statements`, Percona Monitoring and Management.

## Change Management in Production

```text
1. Develop and test the change in development.
2. Verify in staging with production-like data volume.
3. Take or confirm a fresh backup.
4. Apply during an agreed maintenance window.
5. Monitor errors and query performance after release.
6. Keep a tested rollback plan ready.
```

> Operations that look harmless can lock a table. Adding an index or changing a column type on a table with hundreds of millions of rows can block writes for a long time, so large changes use online schema change tools (such as `pt-online-schema-change`, `gh-ost`, or `CREATE INDEX CONCURRENTLY` in PostgreSQL).

## Managed and Cloud Databases

Many enterprises run databases as managed services, where the provider handles patching, backups, and failover.

| Provider | Services |
|---|---|
| AWS | RDS, Aurora, DynamoDB |
| Google Cloud | Cloud SQL, Spanner, BigQuery |
| Azure | Azure SQL Database, Cosmos DB |
| Others | MongoDB Atlas, PlanetScale, Neon, Supabase |

## Data Security and Compliance

| Practice | Description |
|---|---|
| Encryption at rest | Disk or tablespace encryption |
| Encryption in transit | TLS for all connections |
| Network isolation | Database in a private network, not exposed to the internet |
| Auditing | Log who accessed or changed sensitive data |
| Data masking | Hide or scramble sensitive columns outside production |
| Backup encryption | Protect backup files like production data |
| Regulations | GDPR, HIPAA, PCI-DSS, and similar laws shape what can be stored and for how long |

## Application and Database Interaction

| Concept | Description |
|---|---|
| ORM (Object-Relational Mapper) | Library (Hibernate, SQLAlchemy, Django ORM, Prisma) that maps tables to objects and generates SQL |
| N+1 query problem | One query loads a list, then one extra query runs per row; fix with a join or batch fetch |
| Transactions in code | Group related writes so they succeed or fail together |
| Retry logic | Retry on deadlocks and transient connection errors |
| Pagination | Use `LIMIT` with an indexed ordering instead of loading everything |

```sql
SELECT order_id, total_amount
FROM orders
WHERE customer_id = 42
ORDER BY created_at DESC
LIMIT 20 OFFSET 0;
```

## Things Everyone Working with Databases Should Know

```text
1. Production data is real: treat every write as irreversible until proven otherwise.
2. Backups are only useful if restores have been tested.
3. Preview with SELECT before any UPDATE or DELETE.
4. Do not connect to production with personal superuser accounts for routine work.
5. Schema changes go through migrations, version control, and review.
6. Indexes speed up reads but slow down writes and use disk space.
7. Store timestamps in UTC and money as DECIMAL.
8. Never store plaintext passwords or commit credentials to source control.
9. Check execution plans before blaming the database for slowness.
10. Most "database problems" are really query, index, or application problems.
```

**Remember:** A production database is more than tables and queries. It includes environments, access control, migrations, backups, monitoring, and a change process, and these matter as much as SQL knowledge.

---

# Scenario-Based Questions and Answers

## Question 1: Redundant Data Across Tables

**Scenario:** A company stores customer name and address in every order record. When a customer updates their address, only some order records get updated, causing inconsistent data.

**Answer:** This is a data redundancy and update anomaly problem caused by an unnormalized schema. The fix is to normalize the database by creating a separate `Customer` table with a `CustomerID` as the primary key, and referencing it as a foreign key in the `Orders` table. This ensures the address is stored once and updated in a single place.

---

## Question 2: Slow Query Performance

**Scenario:** A `Products` table has a million rows, and a query filtering by `ProductName` is taking several seconds to return results.

**Answer:** The likely cause is a full table scan due to the absence of an index on `ProductName`. Creating a non-clustered index on that column allows the database engine to quickly locate matching rows using an efficient structure like a B+ Tree, instead of scanning every row. Note that indexes should be used judiciously since they slow down write operations.

---

## Question 3: Lost Updates in a Banking System

**Scenario:** Two bank employees simultaneously update the same account balance. One deposits $100 and another withdraws $50, but the final balance only reflects one of the changes.

**Answer:** This is a lost update problem caused by insufficient concurrency control. The solution is to use proper locking mechanisms (such as exclusive locks) or an appropriate isolation level (like Repeatable Read or Serializable) so that one transaction completes fully before the other begins modifying the same data, ensuring both updates are applied correctly.

---

## Question 4: System Crash During a Fund Transfer

**Scenario:** During a transaction that transfers money from Account A to Account B, the system crashes after debiting Account A but before crediting Account B.

**Answer:** This scenario violates the Atomicity property of ACID. On recovery, the DBMS uses transaction logs to identify that the transaction was incomplete and performs a rollback (undo) of the debit operation on Account A, restoring the database to its state before the transaction began, ensuring no partial updates persist.

---

## Question 5: Designing a Many-to-Many Relationship

**Scenario:** A `Student` can enroll in multiple `Course` entries, and each `Course` can have multiple `Student` entries. The design needs to represent this in a relational database.

**Answer:** A direct many-to-many relationship cannot be represented with a simple foreign key. The solution is to create a junction (bridge) table, such as `Enrollment`, containing `StudentID` and `CourseID` as foreign keys referencing the `Student` and `Course` tables respectively, with the combination often serving as a composite primary key.

---

## Question 6: Choosing Between SQL and NoSQL

**Scenario:** A social media application needs to store rapidly growing, unstructured user-generated content such as posts with varying fields, and needs to scale horizontally across many servers.

**Answer:** A NoSQL document-based database (such as MongoDB) is more appropriate here because it offers a flexible schema suited for varying content structures and is designed for horizontal scalability across distributed servers, unlike traditional relational databases which favor fixed schemas and vertical scaling.

---

## Question 7: Preventing Dirty Reads in a Reporting System

**Scenario:** A reporting transaction reads sales data while another transaction is in the middle of updating and has not yet committed. The report shows figures that later get rolled back.

**Answer:** This is a dirty read problem. Setting the transaction isolation level to at least Read Committed ensures that a transaction can only read data that has already been committed by other transactions, preventing the reporting transaction from reading uncommitted, potentially inaccurate data.

---

## Question 8: Deadlock Between Two Transactions

**Scenario:** Transaction 1 locks Row A and waits for Row B, while Transaction 2 locks Row B and waits for Row A, causing both transactions to wait indefinitely.

**Answer:** This is a classic deadlock. The DBMS typically resolves this using deadlock detection (via a wait-for graph) and resolves it by choosing a victim transaction to roll back, releasing its locks so the other transaction can proceed. Application-level solutions include always acquiring locks in a consistent order across transactions to prevent circular waits.

---

## Question 9: Embed or Reference in MongoDB

**Scenario:** A blog platform stores posts and comments. Most posts have a handful of comments, but a few viral posts receive hundreds of thousands. The post page always displays the post together with its latest 10 comments.

**Answer:** Embedding every comment inside the post document would create an unbounded array that can approach the 16 MB document limit and make updates slow. Instead, use the **Subset pattern**: keep only the latest 10 comments embedded in the post (so the page loads with one read), and store all comments in a separate `comments` collection that references the post's `_id`, with an index on `post_id` and `created_at`. This combines fast reads for the common case with unbounded scalability for the full history.

---

## Question 10: Slow MongoDB Query

**Scenario:** A query `db.orders.find({ status: "SHIPPED", customer_id: 42 }).sort({ created_at: -1 })` is slow on a collection with 50 million documents. `explain()` shows `COLLSCAN`.

**Answer:** The query performs a full collection scan because no suitable index exists. Create a compound index following the **ESR rule** (Equality, Sort, Range): `db.orders.createIndex({ customer_id: 1, status: 1, created_at: -1 })`. Both equality fields come first, and the sort field follows, so MongoDB can find matching documents and return them already sorted, avoiding an in-memory sort. Re-run `explain("executionStats")` and confirm an `IXSCAN` with `totalDocsExamined` close to `nReturned`.

---

## Question 11: Choosing a Shard Key

**Scenario:** A logging system plans to shard a collection using the `timestamp` field as the shard key. After launch, one shard is overloaded while the others sit idle.

**Answer:** A monotonically increasing key such as a timestamp sends all new writes to the shard that holds the highest range, creating a **hot shard**. Choose a key with high cardinality and even distribution, such as a **hashed** shard key (`{ device_id: "hashed" }`) or a compound key like `{ device_id: 1, timestamp: 1 }`. Hashing spreads writes evenly, at the cost of making range queries on that field hit multiple shards, so pick the key based on the most common query pattern.

---

## Question 12: Losing Writes After a MongoDB Failover

**Scenario:** An application writes an order to MongoDB with `w: 1`. The primary crashes immediately afterwards, a new primary is elected, and the order has vanished.

**Answer:** With `w: 1`, only the old primary acknowledged the write. If it had not yet replicated to a secondary, the new primary never received it, and when the old primary rejoins, that write is rolled back. Use write concern `w: "majority"` (optionally with `j: true`) for critical data so a write is acknowledged only after a majority of replica set members hold it, guaranteeing it survives a failover. The trade-off is slightly higher write latency.

---

## Question 13: Reducing Database Load with a Cache

**Scenario:** A product detail page runs the same expensive SQL query thousands of times per minute, even though product data changes only a few times a day. The database CPU is near its limit.

**Answer:** Introduce Redis as a cache using the **cache-aside pattern**: the application checks Redis first (`GET cache:product:42`); on a miss it queries the database, stores the result in Redis with a TTL (for example `SET ... EX 600`), and returns it. When a product is updated, delete its cache key so the next read refreshes it. This removes the vast majority of identical database reads and cuts latency to sub-millisecond for cache hits. Add small random jitter to TTLs to avoid many keys expiring together.

---

## Question 14: Cache Stampede on a Popular Key

**Scenario:** A cached homepage key expires at noon. Within a second, thousands of requests miss the cache simultaneously and all hit the database, which slows to a crawl.

**Answer:** This is a **cache breakdown (stampede)**. Fixes include: (1) use a short-lived lock (`SET lock:home <token> NX PX 5000`) so only one request rebuilds the value while others wait briefly or serve slightly stale data; (2) refresh the cache proactively in the background before it expires; (3) avoid TTLs on the very hottest keys and update them explicitly; and (4) add random jitter to TTLs so related keys do not expire at the same instant.

---

## Question 15: Implementing a Rate Limiter

**Scenario:** An API must allow each user at most 100 requests per minute and reject anything beyond that.

**Answer:** Use a Redis counter per user per time window. On each request run `INCR rate:user:{id}:{minute}`; if the returned value is `1`, also run `EXPIRE` on that key for 60 seconds. If the counter exceeds 100, reject the request. `INCR` is atomic, so concurrent requests are counted correctly. To avoid a gap between `INCR` and `EXPIRE` and to prevent boundary bursts, wrap the logic in a Lua script, or use a sliding window with a sorted set that stores request timestamps.

---

## Question 16: Building a Real-Time Leaderboard

**Scenario:** A mobile game needs a leaderboard showing the top 10 players and each player's current rank, updated instantly as scores change across millions of players.

**Answer:** Use a Redis **Sorted Set**. Update scores with `ZINCRBY game:lb 50 player42` (or `ZADD`), fetch the top 10 with `ZRANGE game:lb 0 9 REV WITHSCORES`, and get a player's rank with `ZREVRANK game:lb player42`. Sorted set operations are O(log N), so the leaderboard stays fast at millions of members. Persist the authoritative scores in the main database as well, since Redis is the fast serving layer.

---

## Question 17: Redis Runs Out of Memory

**Scenario:** A Redis instance used as a cache starts returning "OOM command not allowed when used memory > 'maxmemory'" errors on writes.

**Answer:** The instance has reached `maxmemory` and uses the default `noeviction` policy, so it rejects writes instead of removing old data. For a cache, set `maxmemory-policy allkeys-lru` (or `allkeys-lfu`) so Redis evicts the least useful keys automatically. Then investigate the cause: ensure keys have TTLs, look for big keys with `redis-cli --bigkeys`, check `evicted_keys` and the hit ratio, and increase memory or move to Redis Cluster if the working set genuinely exceeds one node's RAM.

---

## Question 18: Choosing Between MongoDB and Redis

**Scenario:** An e-commerce application needs to store product catalog data with varying attributes per category, and also needs fast shopping-cart sessions and a "recently viewed items" list per user.

**Answer:** Use both, each for the job it does best. **MongoDB** is the primary store for the product catalog because documents handle varying attributes naturally and support rich queries and indexes. **Redis** handles the shopping-cart session (a Hash with a TTL) and the "recently viewed" list (a List with `LPUSH` + `LTRIM`) because these are small, hot, short-lived data needing sub-millisecond access. Redis can also cache frequently requested catalog pages in front of MongoDB. MongoDB remains the durable source of truth.