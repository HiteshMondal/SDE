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

## Categories of SQL Commands

### DDL (Data Definition Language)
Defines database structure. Commands: `CREATE`, `ALTER`, `DROP`, `TRUNCATE`.

### DML (Data Manipulation Language)
Manipulates data within tables. Commands: `SELECT`, `INSERT`, `UPDATE`, `DELETE`.

### DCL (Data Control Language)
Controls access permissions. Commands: `GRANT`, `REVOKE`.

### TCL (Transaction Control Language)
Manages transactions. Commands: `COMMIT`, `ROLLBACK`, `SAVEPOINT`.

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

## Subqueries

A query nested inside another query, used in `SELECT`, `WHERE`, or `FROM` clauses to filter or compute values based on another dataset.

## Aggregate Functions

Functions like `COUNT()`, `SUM()`, `AVG()`, `MIN()`, and `MAX()` that operate on a set of values and return a single summarized value, often used with `GROUP BY` and filtered using `HAVING`.

## Views

A virtual table based on the result of a stored SQL query. Views do not store data themselves (in most cases) but simplify complex queries and add a layer of abstraction and security.

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
