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
