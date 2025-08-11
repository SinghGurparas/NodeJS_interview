# **indexes in DATABASES**

## **1. MongoDB**

**What is an index?**
In MongoDB, an index is a special data structure (B-tree for most indexes) that stores a small portion of the collection’s data in an easy-to-search form, improving query performance.

**Common Index Types:**

| Index Type             | Purpose                                                | Example                                                   |
| ---------------------- | ------------------------------------------------------ | --------------------------------------------------------- |
| **Single Field**       | Index on one field                                     | `{ name: 1 }` — ascending on `name`                       |
| **Compound**           | Index on multiple fields                               | `{ name: 1, age: -1 }` — first sort by `name`, then `age` |
| **Multikey**           | For array fields, creates index for each array element | `tags: [ "node", "mongodb" ]`                             |
| **Text**               | For text search across strings                         | `{ description: "text" }`                                 |
| **Hashed**             | For sharding or hashing values                         | `{ userId: "hashed" }`                                    |
| **Wildcard**           | Index on dynamic/unknown fields                        | `{ "$**": 1 }`                                            |
| **TTL (Time-to-Live)** | Auto-delete documents after a certain time             | `{ createdAt: 1 }` with `expireAfterSeconds`              |

**Example:**

```javascript
// Collection: users
[
  { _id: 1, name: "Alice", age: 25, tags: ["music", "travel"], createdAt: new Date() },
  { _id: 2, name: "Bob", age: 30, tags: ["coding", "mongodb"], createdAt: new Date() }
]

// Index example:
db.users.createIndex({ name: 1 }) // single-field index on name
```

---

## **2. MySQL**

**What is an index?**
In MySQL, an index is a data structure (B-tree, sometimes Hash) that improves the speed of `SELECT` queries and sometimes ordering, at the cost of extra storage and slower writes.

**Common Index Types:**

| Index Type      | Purpose                             | Example                    |
| --------------- | ----------------------------------- | -------------------------- |
| **PRIMARY**     | Uniquely identifies each row        | `PRIMARY KEY (id)`         |
| **UNIQUE**      | No duplicate values                 | `UNIQUE (email)`           |
| **INDEX / KEY** | Non-unique index for faster lookups | `INDEX (name)`             |
| **FULLTEXT**    | Full-text search in text columns    | `FULLTEXT (description)`   |
| **SPATIAL**     | For geometry data types             | `SPATIAL INDEX (location)` |

**Example:**

```sql
CREATE TABLE users (
    id INT PRIMARY KEY,
    name VARCHAR(50),
    email VARCHAR(50) UNIQUE,
    description TEXT,
    INDEX(name),           -- normal index
    FULLTEXT(description)  -- full-text search
);

INSERT INTO users VALUES 
(1, 'Alice', 'alice@example.com', 'Alice loves music and travel'),
(2, 'Bob', 'bob@example.com', 'Bob codes in Node.js and MongoDB');
```

---

## **3. Redis**

Redis is an **in-memory key-value store**, so it doesn’t have “traditional” indexes like SQL or MongoDB. Instead, it uses **data structures** and special commands to simulate indexing.

**Ways Redis achieves index-like behavior:**

| Method                       | Purpose                                                     | Example                      |
| ---------------------------- | ----------------------------------------------------------- | ---------------------------- |
| **Sorted Sets (ZSET)**       | Maintain ordered data by score (can be an index)            | ZRANGE for range queries     |
| **Secondary Index via Sets** | Store keys matching certain field values                    | Maintain manual index set    |
| **RediSearch Module**        | Provides real indexing & full-text search                   | Automatically indexes hashes |
| **HASH Fields**              | Store structured data, then index manually using sets/zsets | Key-value mapping per field  |

**Example using Sorted Set as an index:**

```redis
# Store user data
HMSET user:1 name "Alice" age 25
HMSET user:2 name "Bob" age 30

# Create an index on age
ZADD ageIndex 25 user:1
ZADD ageIndex 30 user:2

# Query users by age range
ZRANGEBYSCORE ageIndex 20 28
# → returns ["user:1"]
```

---

### **Comparison Table**

| Feature        | MongoDB                                                 | MySQL                                      | Redis (core)                         |
| -------------- | ------------------------------------------------------- | ------------------------------------------ | ------------------------------------ |
| Index types    | Single, compound, multikey, text, hashed, wildcard, TTL | Primary, unique, normal, fulltext, spatial | Sorted sets, manual sets, RediSearch |
| Auto index     | `_id` field                                             | PRIMARY KEY                                | None                                 |
| Text search    | Text index                                              | FULLTEXT                                   | RediSearch                           |
| Array indexing | Multikey                                                | Not native                                 | Manual                               |
| Expiry index   | TTL index                                               | No direct (use job)                        | Key TTL                              |

---

If you want, I can make a **single table with records** and then **show how that same data is indexed differently** in **MongoDB, MySQL, and Redis**, so you can compare them visually in one go.
That way you’ll see the “same problem, different database” approach. Would you like me to do that?
