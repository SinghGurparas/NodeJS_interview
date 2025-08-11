# RAW SQL VS ORMS

## **1. What is Raw SQL?**

Raw SQL means you **manually write SQL queries** (e.g., `SELECT * FROM users WHERE id = 1;`) and send them to the database directly, often through a driver like `mysql2`, `pg`, or `node-mysql`.

**Pros:**

* **Full control**: You can use any SQL feature supported by the DB.
* **Performance optimization**: Write super-optimized queries (e.g., fine-tuned joins, indexes, CTEs).
* **No abstraction overhead**: Faster execution, no ORM translation layer.

**Cons:**

* **Verbose & repetitive**: CRUD boilerplate everywhere.
* **Error-prone**: More manual handling for SQL injection prevention, mapping results, migrations.
* **DB-specific**: Locks you into one SQL dialect.

---

## **2. What is an ORM?**

ORM (Object Relational Mapping) is a library that **maps database tables to programming language objects**.
Examples in Node.js: Sequelize, TypeORM, Prisma, Objection.js.

Instead of writing SQL, you write **JavaScript methods** that ORM translates to SQL.

**Example:**

```js
// ORM style
User.findOne({ where: { id: 1 } });

// Raw SQL style
await db.query('SELECT * FROM users WHERE id = ?', [1]);
```

**Pros:**

* **Developer productivity**: Less boilerplate, readable.
* **Cross-database portability**: Same API for MySQL/PostgreSQL/SQLite.
* **Built-in safety**: Escaping params to prevent SQL injection.
* **Relations & migrations**: Easier joins, schema sync, associations.

**Cons:**

* **Performance overhead**: ORM-generated queries may be inefficient.
* **Learning curve**: You need to learn ORM’s own API on top of SQL.
* **Complex queries**: Sometimes ORM syntax is harder than SQL.

---

## **3. Key Interview Concepts & Lingo**

If this comes up in an interview, expect **follow-up questions** like:

| Term / Concept            | Meaning                                                               | Example Question                                                    |
| ------------------------- | --------------------------------------------------------------------- | ------------------------------------------------------------------- |
| **SQL Injection**         | Malicious SQL inserted into queries. Raw SQL needs manual prevention. | “How does ORM prevent SQL injection?”                               |
| **Query Builder**         | A middle-ground: programmatic SQL without full ORM.                   | “What’s the difference between ORM and Query Builder like Knex.js?” |
| **Eager vs Lazy Loading** | How related data is fetched (all at once vs on demand).               | “How does your ORM handle joins?”                                   |
| **N+1 Problem**           | ORM inefficiency when loading related data in loops.                  | “How would you prevent N+1 queries in Sequelize/TypeORM?”           |
| **Parameterized Queries** | SQL with placeholders to prevent injection.                           | “Show me a safe query in raw SQL.”                                  |
| **Migrations**            | Scripts to evolve DB schema.                                          | “How does your ORM handle migrations?”                              |
| **Dialect**               | SQL syntax variations across DBs.                                     | “What happens if you change DB vendor with ORM?”                    |
| **Transactions**          | Group multiple operations into a single atomic block.                 | “How do you handle transactions in ORM vs raw SQL?”                 |
| **Connection Pooling**    | Reusing DB connections for performance.                               | “Is pooling handled differently in ORM?”                            |
| **Mapping**               | Conversion between DB rows and objects.                               | “How does ORM map data types between SQL and JS?”                   |

---

### **1. SQL Injection — “How does ORM prevent SQL injection?”**

**Answer:**
SQL Injection happens when untrusted input is directly concatenated into a query string, letting attackers run arbitrary SQL.
ORMs prevent it by **parameterizing queries** — instead of inserting raw text, they use placeholders and bind values separately. This way, the DB engine treats the value purely as data, not code.

**Example in Sequelize:**

```js
User.findOne({ where: { id: userInputId } });
```

This internally generates:

```sql
SELECT * FROM users WHERE id = ?  -- value is bound, not concatenated
```

---

### **2. Query Builder — “What’s the difference between ORM and Query Builder like Knex.js?”**

**Answer:**

* **Query Builder**: Lets you build SQL queries programmatically (e.g., method chaining), but you still think in SQL terms — no automatic mapping of results to models/objects.
* **ORM**: Adds a mapping layer — database tables become JavaScript classes with CRUD methods, relationships, and lifecycle hooks.

Think of a Query Builder as **"SQL with helper functions"**, while an ORM is **"SQL + an object mapping abstraction"**.

---

## **3. Eager vs Lazy Loading — “How does your ORM handle joins?”**

**Answer:**

* **Eager Loading**: Fetches related data immediately in a single query (often using JOINs).
* **Lazy Loading**: Fetches related data only when you explicitly request it, usually via a separate query.

**Example in Sequelize:**

```js
// Eager loading
User.findAll({ include: Post }); // Single SQL with JOIN

// Lazy loading
const user = await User.findByPk(1);
const posts = await user.getPosts(); // Separate SQL
```

**Trade-off**: Eager loading reduces queries but may fetch unused data; lazy loading is flexible but risks **N+1 queries**.

---

## **4. N+1 Problem — “How would you prevent N+1 queries in Sequelize/TypeORM?”**

**Answer:**
The **N+1 problem** occurs when fetching a list of records triggers a separate query for each record’s related data.
**Fix**: Use **eager loading** or **batch fetching**.

Example:

```js
// BAD: N+1 Problem
const users = await User.findAll();
for (const user of users) {
  await user.getPosts(); // One query per user
}

// GOOD: Eager loading
const users = await User.findAll({ include: Post });
```

---

## **5. Parameterized Queries — “Show me a safe query in raw SQL.”**

**Answer:**
Parameterized queries use placeholders and bind variables separately, avoiding injection.

```js
// mysql2
const [rows] = await db.query('SELECT * FROM users WHERE id = ?', [userId]);
```

Here, `?` is replaced safely with `userId`, without altering the SQL syntax.

---

## **6. Migrations — “How does your ORM handle migrations?”**

**Answer:**
Migrations are version-controlled scripts that apply schema changes (tables, columns, indexes) incrementally.
Most ORMs include a CLI for:

* `generate migration`: Create a migration file from schema changes.
* `run migration`: Apply changes to the database.
* `revert migration`: Roll back a change.

Example (Sequelize):

```bash
npx sequelize-cli migration:generate --name add-age-to-users
npx sequelize-cli db:migrate
```

---

## **7. Dialect — “What happens if you change DB vendor with ORM?”**

**Answer:**
ORMs abstract SQL dialect differences (e.g., MySQL vs PostgreSQL syntax). If you switch DB vendor:

* The ORM translates queries into the new dialect.
* Some DB-specific features (like JSON columns, full-text search) may break or need rewriting.
* You may need to reconfigure connection settings and run compatible migrations.

---

## **8. Transactions — “How do you handle transactions in ORM vs raw SQL?”**

**Answer:**
Transactions group multiple queries into an **atomic block** — either all succeed or all fail.

**Raw SQL:**

```js
await db.beginTransaction();
await db.query('UPDATE accounts SET balance = balance - 100 WHERE id = 1');
await db.query('UPDATE accounts SET balance = balance + 100 WHERE id = 2');
await db.commit();
```

**Sequelize:**

```js
await sequelize.transaction(async (t) => {
  await Account.update({ balance: balance - 100 }, { where: { id: 1 }, transaction: t });
  await Account.update({ balance: balance + 100 }, { where: { id: 2 }, transaction: t });
});
```

---

## **9. Connection Pooling — “Is pooling handled differently in ORM?”**

**Answer:**
Connection pooling keeps DB connections open for reuse instead of reconnecting for every request.
Most ORMs handle pooling internally using their underlying DB driver. You configure it in the ORM settings:

Example (Sequelize):

```js
new Sequelize(DB, USER, PASS, {
  pool: { max: 10, min: 0, idle: 10000 }
});
```

With raw SQL, you’d configure pooling directly in the driver.

---

## **10. Mapping — “How does ORM map data types between SQL and JS?”**

**Answer:**
ORMs automatically convert DB types into JS equivalents:

* `INTEGER` → `number`
* `VARCHAR` → `string`
* `BOOLEAN` → `true/false`
* `TIMESTAMP` → JS `Date` object

They handle serialization/deserialization behind the scenes so you work with native JS types instead of parsing them manually.

---

If you want, I can also make you a **one-page “Raw SQL vs ORM Interview Battle Card”** where I compress all this into a single visual table you can recall instantly during interviews.
Do you want me to prepare that?

---

## **4. When to Use Raw SQL vs ORM**

**Use Raw SQL when:**

* You need **max performance** and fine control.
* Queries are **complex** (window functions, recursive CTEs).
* You’re **DB-specific** and not planning to switch vendors.

**Use ORM when:**

* You want **rapid development** and maintainability.
* You work with **complex relationships** in a business domain.
* You need **cross-DB support**.
* Team members are **not SQL experts**.

---

## **5. Example Comparison**

### Raw SQL

```js
const [rows] = await db.query('SELECT name FROM users WHERE active = ?', [true]);
```

### ORM (Sequelize)

```js
const users = await User.findAll({ where: { active: true }, attributes: ['name'] });
```

---

## **6. Interview Pro Tips**

* **Don’t badmouth ORMs** — acknowledge they trade some performance for productivity.
* Mention **Hybrid Approach**: Many devs use ORM for CRUD but raw SQL for complex queries.
* Be ready to show **how ORM-generated SQL looks** (many ORMs allow `.toSQL()` or logging queries).
* Mention **Query Optimization**: Even with ORM, you should inspect and index for slow queries.

---
