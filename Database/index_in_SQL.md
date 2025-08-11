In SQL (and specifically in **MySQL / relational databases**), indexes are categorized based on **purpose, uniqueness, and data structure**.

Here’s the breakdown of **index types in SQL** with examples:

---

## **1. Primary Index**

* **Definition:** Automatically created when you define a `PRIMARY KEY`.
* **Uniqueness:** Always unique, cannot be NULL.
* **Data Structure:** Usually B-Tree.
* **Example:**

```sql
CREATE TABLE users (
    id INT PRIMARY KEY,
    name VARCHAR(50)
);
```

Here, `id` has a **primary index**.

---

## **2. Unique Index**

* **Definition:** Ensures all values in the column(s) are unique, but allows one NULL (in MySQL).
* **Use Case:** Email addresses, usernames, etc.
* **Example:**

```sql
CREATE UNIQUE INDEX idx_email ON users(email);
```

---

## **3. Non-Unique (Normal) Index**

* **Definition:** Speeds up searches but does not enforce uniqueness.
* **Use Case:** Frequently queried columns like `name` or `created_at`.
* **Example:**

```sql
CREATE INDEX idx_name ON users(name);
```

---

## **4. Composite (Multi-Column) Index**

* **Definition:** Index on two or more columns, improves queries using **both** columns in the WHERE clause.
* **Note:** Column order in the index matters.
* **Example:**

```sql
CREATE INDEX idx_name_age ON users(name, age);
```

---

## **5. Full-Text Index**

* **Definition:** Special index for **full-text search** on text columns.
* **Use Case:** Searching keywords in long text fields.
* **Example:**

```sql
CREATE FULLTEXT INDEX idx_desc ON articles(description);
```

* **Query Example:**

```sql
SELECT * FROM articles WHERE MATCH(description) AGAINST('music');
```

---

## **6. Spatial Index**

* **Definition:** Index for spatial (geographic) data types like `POINT`, `LINESTRING`, `POLYGON`.
* **Use Case:** GIS queries (maps, location search).
* **Example:**

```sql
CREATE SPATIAL INDEX idx_location ON places(location);
```

---

## **7. Clustered vs. Non-Clustered (Conceptual)**

* **Clustered Index:** The table’s data is physically stored in the order of this index (in MySQL’s InnoDB, the PRIMARY KEY is clustered).
* **Non-Clustered Index:** Separate structure from the data; contains pointers to the data.

Example in InnoDB:

* PRIMARY KEY → **Clustered Index**
* All others → **Non-Clustered Index**

---

### **Quick Reference Table**

| Index Type | Unique?  | Supports NULL? | Purpose                       |
| ---------- | -------- | -------------- | ----------------------------- |
| Primary    | ✅ Yes    | ❌ No           | Identify rows uniquely        |
| Unique     | ✅ Yes    | ✅ Yes (1 NULL) | Prevent duplicates            |
| Non-Unique | ❌ No     | ✅ Yes          | Speed up lookups              |
| Composite  | Optional | ✅ Yes          | Speed up multi-column queries |
| Full-Text  | ❌ No     | ✅ Yes          | Search text efficiently       |
| Spatial    | ❌ No     | ✅ Yes          | Index geographical data       |

---

If you want, I can also make a **sample table** and show **how different SQL index types would look and be used** on that one dataset, so you can memorize it faster for interviews. That would make it easier to explain in real-time.
