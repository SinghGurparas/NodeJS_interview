# Databases (Mongo and SQL)

- MySQL is a Relational Database with a rigid, tabular schema.
- MongoDB is a NoSQL Document Database with a flexible, schema-less structure.

## Keywords

## MONGO VS MYSQL

Here's a breakdown of the key differences and my reasoning for choosing each one:

### **1. Data Model and Schema**

- **MySQL (Relational):**
  - **Data Model:** Stores data in structured tables with rows and columns.
  - **Schema:** Requires a predefined, strict schema. Every row in a table must conform to the same columns and data types. This is great for ensuring data integrity and consistency.
  - **Node.js Context:** You'd typically use an Object-Relational Mapper (ORM) like Sequelize or Knex to interact with this structure, which maps JavaScript objects to database tables.

- **MongoDB (NoSQL/Document-based):**
  - **Data Model:** Stores data in flexible, JSON-like 'documents' within collections.
  - **Schema:** Does not enforce a rigid schema at the database level. Each document in a collection can have a different structure. This provides immense flexibility for rapidly evolving applications.
  - **Node.js Context:** This aligns perfectly with Node.js because data is already in a JSON-native format. ***You'd use an Object-Document Mapper (ODM) like Mongoose, which allows you to define a schema in your application code for better data validation and structure.***

### **2. Relationships and Joins**

- **MySQL:**
  - **Relationships:** Built for handling complex relationships between different tables using foreign keys. This is a core feature of relational databases.
  - **Joins:** Uses powerful `JOIN` operations (e.g., `INNER JOIN`, `LEFT JOIN`) to combine data from multiple tables in a single query.
  - **Benefit:** Ideal for applications with a lot of interconnected data, like an e-commerce store with tables for `customers`, `orders`, and `products`.

- **MongoDB:**
  - **Relationships:** Relationships are handled either by **embedding** related data inside a single document or by **referencing** other documents using their IDs.
  - **Joins:** Traditionally, MongoDB did not have native `JOINs`. While it now has the `$lookup` aggregation stage, it's not as powerful or performant as a relational `JOIN` for complex multi-table relationships. The best practice is to design your data model to minimize the need for joins.
  - **Benefit:** This is a strength for applications where related data can be self-contained within a single document, reducing the need for multiple queries and improving read performance.

### **3. Scalability**

- **MySQL:**
  - **Scaling:** Primarily scales **vertically** (adding more CPU, RAM, and storage to a single, more powerful server). While horizontal scaling via replication and sharding is possible, it's often more complex to set up and manage.
  - **Use Case:** Good for applications that can handle their load on a single powerful server.

- **MongoDB:**
  - **Scaling:** Designed for **horizontal scaling** by default. It has built-in features like `Replica Sets` (for high availability and read scaling) and `Sharding` (for distributing data across multiple servers).
  - **Use Case:** Excellent for applications that need to handle massive volumes of data and a high number of concurrent users, as it allows you to distribute the load across many commodity servers.

### **4. When to Choose Which (Project-based Rationale)**

**Choose MySQL when:**

- **You need strong transactional integrity.** For applications where data consistency is absolutely critical, such as **financial systems, banking applications, or e-commerce orders**. MySQL's `ACID` properties guarantee that a series of operations either all succeed or all fail, which is non-negotiable for these use cases.
- **Your data has a well-defined, consistent structure.** If your data is highly structured and not expected to change much over time (e.g., user profiles with a fixed set of fields).
- **You rely on complex reporting and analytics.** Relational databases are built for complex queries with many joins, making them a great fit for traditional Business Intelligence (BI) tools.

**Choose MongoDB when:**

- **You prioritize rapid development and have an evolving schema.** This is perfect for startups, prototypes, or agile projects where the data model changes frequently. You can add new fields to documents without needing to perform a costly schema migration.
- **Your data is unstructured or semi-structured.** For applications dealing with things like **IoT sensor data, real-time analytics, user-generated content, or log data**, where each data point might have a slightly different structure.
- **You need high read performance and horizontal scalability.** For applications that need to handle a high volume of read operations and are expected to grow to a massive scale, like a **content management system (CMS), social media feed, or a mobile app backend**.

**Final Answer Summary:** "In summary, I would choose **MySQL** for applications where data integrity and complex, interconnected relationships are the top priority. I would choose **MongoDB** for applications where flexibility, scalability, and handling unstructured data are more important, especially in the context of rapid development, which often aligns well with the Node.js ecosystem."

## Normalization in MYSQL

Normalization: "Normalization is the process of organizing your database to reduce data redundancy and improve data integrity. You break down large tables into smaller, related tables and use relationships (like foreign keys) between them. The goal is that each piece of data is stored only once.

- **Benefits:**

    Prevents update anomalies (where you might update one record but forget another), and generally keeps data accurate.

- **When to use :**

    Good for transactional systems (e.g., e-commerce orders, banking) where data accuracy and consistency are paramount, and write operations are frequent.

### First Normal Form (1NF)

**Rule:** A table is in 1NF if it meets two conditions:

1. All columns contain atomic values (each cell holds only one piece of data, not a list).
2. Each row is unique.

**Example:** We split the repeating group of courses into separate rows.

**Table: `student_courses_1nf`**

| StudentID | StudentName | CourseID | CourseName | Professor | Grade |
|---|---|---|---|---|---|
| 1 | Alice | 101 | Intro to DB | Dr. Smith | A |
| 1 | Alice | 102 | Data Structures | Dr. Jones | B |
| 2 | Bob | 101 | Intro to DB | Dr. Smith | C |
| 2 | Bob | 103 | Algorithms | Dr. White | A |

(Note: This table is already in 1NF from our UNF example because the data was structured this way. The key is that there are no "lists" or "arrays" within a single cell.)

---

### Second Normal Form (2NF)

**Rule:** A table is in 2NF if it meets these conditions:

1. It is in 1NF.
2. All non-key attributes (columns that are not part of the primary key) are fully dependent on the entire primary key. This applies only to tables with a composite primary key (a primary key made of two or more columns).

**Example:**

- **Primary Key:** `(StudentID, CourseID)`
- `StudentName` depends only on `StudentID`, not on `CourseID`. This is a **partial dependency**.
- `CourseName` and `Professor` depend only on `CourseID`. This is also a **partial dependency**.

**Solution:** We break the table into three separate tables to eliminate these partial dependencies.

1. **`students` Table:**

    | StudentID | StudentName |
    |---|---|
    | 1 | Alice |
    | 2 | Bob |

2. **`courses` Table:**

    | CourseID | CourseName | Professor |
    |---|---|---|
    | 101 | Intro to DB | Dr. Smith |
    | 102 | Data Structures | Dr. Jones |
    | 103 | Algorithms | Dr. White |

3. **`enrollments` Table:** (The linking table with the composite primary key)

    | StudentID | CourseID | Grade |
    |---|---|---|
    | 1 | 101 | A |
    | 1 | 102 | B |
    | 2 | 101 | C |
    | 2 | 103 | A |

---

### Third Normal Form (3NF)

**Rule:** A table is in 3NF if it meets these conditions:

1. It is in 2NF.
2. There are no transitive dependencies. A transitive dependency occurs when a non-key attribute is dependent on another non-key attribute.

**Example:** Let's look at the `courses` table from 2NF:

**`courses` Table**

| CourseID | CourseName | Professor |
|---|---|---|
| 101 | Intro to DB | Dr. Smith |
| 102 | Data Structures | Dr. Jones |
| 103 | Algorithms | Dr. White |

- **Primary Key:** `CourseID`
- **Non-key attributes:** `CourseName`, `Professor`
- `Professor` is not dependent on `CourseID` but is dependent on `CourseName`. For example, "Intro to DB" is always taught by "Dr. Smith". This is a **transitive dependency**.
- (Okay, that's not a great example. A better one would be if we had a `ProfessorID` and `Professor` in the table. The `Professor` name would depend on the `ProfessorID` which in turn depends on the `CourseID`.)

**Let's use a better example:** Imagine the `courses` table also had a `ProfessorOffice` column.
`courses` Table:

| CourseID | CourseName | Professor | ProfessorOffice |
|---|---|---|---|
| 101 | Intro to DB | Dr. Smith | A101 |
| 102 | Data Structures | Dr. Jones | A102 |

- `ProfessorOffice` depends on `Professor` (a non-key attribute), which in turn depends on `CourseID`. This is a **transitive dependency**.

**Solution:** We move the `ProfessorOffice` to a new `professors` table.

1. **`courses` Table (modified):**

    | CourseID | CourseName | ProfessorID |
    |---|---|---|
    | 101 | Intro to DB | 1 |
    | 102 | Data Structures | 2 |

2. **`professors` Table:**

    | ProfessorID | ProfessorName | ProfessorOffice |
    |---|---|---|
    | 1 | Dr. Smith | A101 |
    | 2 | Dr. Jones | A102 |

This eliminates the transitive dependency and puts the database in 3NF. Most databases are designed up to 3NF, as it provides a good balance between data integrity and performance.

## Transactions and ACID Properties

1. **Transactions:**

   A transaction is a single logical unit of work performed on a database. It's a sequence of one or more SQL statements (like INSERT, UPDATE, DELETE) that are treated as a single, indivisible operation. Either all the statements within a transaction succeed and are committed to the database, or if any part fails, the entire transaction is rolled back, leaving the database unchanged.

2. **ACID Properties:**

    ACID is an acronym that describes the key properties guaranteeing data integrity in a transactional database. They are crucial for reliability:

    - **Atomicity:**

        (All or Nothing) - This means a transaction is treated as a single, indivisible unit. Either all the operations within it complete successfully, or none of them do. If any part fails, the entire transaction is rolled back. Example: Transferring money from account A to account B. You deduct from A and add to B. If adding to B fails, the deduction from A is also undone.

    - **Consistency:**

        (Valid State) - A transaction brings the database from one valid state to another valid state. It ensures that data rules (like unique constraints, foreign key relationships) are always maintained. Example: If you try to insert a record that violates a unique constraint, the transaction will be rolled back, preventing the database from entering an invalid state.

    - **Isolation:**

        (Independent Execution) - Transactions execute independently of each other. Changes made by one transaction are not visible to other concurrent transactions until the first transaction is committed. This prevents weird results from concurrent operations. Example: If two users try to update the same product's stock simultaneously, isolation ensures that their updates don't interfere with each other and the final stock count is correct.

    - **Durability:**

        (Permanent Changes) - Once a transaction is committed, its changes are permanent and will survive system failures (like power outages). The committed data is written to persistent storage. Example: After a customer successfully places an order and the transaction is committed, even if the server crashes right after, the order data will still be in the database when it restarts."

Importance for Node.js: "For Node.js applications, especially those dealing with critical data like financial transactions, inventory management, or user data, ACID properties are vital. They ensure that even with multiple users or concurrent operations, your data remains accurate, consistent, and reliable, preventing data corruption and business logic errors.
