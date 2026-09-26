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

### Second Normal Form (2NF)
A table is in 2NF if it is in 1NF and every non-key attribute is fully functionally dependent on the entire primary key (removes partial dependency, relevant for composite keys).

### Third Normal Form (3NF)
A table is in 3NF if it is in 2NF and has no transitive dependency — non-key attributes must depend only on the primary key, not on other non-key attributes.

### Boyce-Codd Normal Form (BCNF)
A stricter version of 3NF where for every functional dependency `A → B`, A must be a super key. Handles certain anomalies that 3NF does not.

### Fourth Normal Form (4NF)
Removes multivalued dependencies — a table should not contain two or more independent multivalued facts about an entity.

### Multivalued Dependency (MVD)

A dependency where one attribute determines a set of values for another attribute, independent of other attributes in the relation — the basis for 4NF violations.

### Fifth Normal Form (5NF / PJNF)

A table is in 5NF if it is in 4NF and cannot be decomposed into smaller tables without loss of data (i.e., it has no join dependency that isn't implied by candidate keys).

### Lossless Decomposition and Dependency Preservation

- **Lossless Join Decomposition**: Joining the decomposed tables back together must reproduce the original relation exactly, with no spurious tuples.
- **Dependency Preservation**: All functional dependencies of the original relation must still be enforceable without needing a join.

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
