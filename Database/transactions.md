# Transactions

## **1. MySQL Transactions**

**Definition:**
A transaction is a sequence of SQL statements that are executed as a single unit of work — either **all succeed** (commit) or **all fail** (rollback).
They follow **ACID** properties.

**Key Points:**

* Requires **transactional storage engine** like InnoDB.
* Controlled with `START TRANSACTION`, `COMMIT`, `ROLLBACK`.

**Example:**

```sql
START TRANSACTION;

UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;

COMMIT;   -- Save changes
-- ROLLBACK; -- Undo if error
```

---

## **2. MongoDB Transactions**

**Definition:**
MongoDB originally only had **atomic operations on a single document**.
Since v4.0, it supports **multi-document transactions** in **replica sets** (and sharded clusters since v4.2).

**Key Points:**

* Start a session and transaction.
* All writes inside the transaction commit or abort together.
* Still maintains document-level atomicity for single-doc operations.

**Example (Node.js):**

```javascript
const session = await client.startSession();
session.startTransaction();
try {
    await accounts.updateOne({ _id: 1 }, { $inc: { balance: -100 } }, { session });
    await accounts.updateOne({ _id: 2 }, { $inc: { balance: 100 } }, { session });

    await session.commitTransaction(); // commit all
} catch (e) {
    await session.abortTransaction();  // rollback all
}
session.endSession();
```

---

## **3. Redis Transactions**

**Definition:**
A transaction in Redis is a **sequence of commands** executed in order, without interruption by other clients.
Redis transactions are **not fully ACID** — they guarantee **atomic execution of the command queue** but **not rollback** if one command fails (unless using Lua scripts).

**Key Points:**

* Use `MULTI` to start, `EXEC` to run.
* `WATCH` is used for optimistic locking (abort if watched key changes).
* No partial rollback — if one command fails, others still execute.

**Example:**

```redis
MULTI
DECRBY account:1 100
INCRBY account:2 100
EXEC
```

With optimistic locking:

```redis
WATCH account:1
MULTI
DECRBY account:1 100
INCRBY account:2 100
EXEC  # Fails if account:1 was changed before EXEC
```

---

### **Comparison Table**

| Feature              | MySQL                             | MongoDB                       | Redis                              |
| -------------------- | --------------------------------- | ----------------------------- | ---------------------------------- |
| Multi-record support | ✅ Yes                             | ✅ Since v4.0                  | ⚠️ Yes, but no rollback on failure |
| Atomicity            | ✅ Full (ACID)                     | ✅ Full (ACID in transactions) | ✅ Per transaction block            |
| Rollback             | ✅ Yes                             | ✅ Yes                         | ❌ No (unless Lua scripting)        |
| Isolation levels     | ✅ Multiple (READ COMMITTED, etc.) | ⚠️ Limited control            | ❌ Not configurable                 |
| Typical usage        | Financial ops, orders             | Multi-collection updates      | Bulk updates, counters, caching    |

---

If you want, I can also make a **"money transfer" example** showing the *same transaction implemented in MySQL, MongoDB, and Redis* side-by-side so you can directly compare them during an interview.
It’ll make your answer look **super structured and practical**. Would you like me to make that?
