
# **Database Optimization Basics: Indexing, Normalization, and Caching**

### *Rodgers Wisdom - Saturday 19th 2024*

---

## **1. Introduction**

In today's data-driven applications, optimizing database performance is crucial to ensure fast and efficient data retrieval. This document explores three essential techniques: Indexing, Normalization, and Caching. These strategies can significantly reduce query execution times and improve the overall performance of your database.

In this document, we cover the basics of three key database optimization techniques: **Indexing**, **Normalization**, and **Caching**. Each section includes basic concepts, examples, and illustrations using tables.

---

## **2. Indexing**

### **2.1 What is Indexing?**

Indexing is a technique used to speed up the retrieval of records from a database by minimizing the number of rows that need to be scanned. 

It works similarly to an index in a book: instead of going through every page to find a topic, you can look it up directly in the index.

### **2.2 How Indexes Work**

An index creates a data structure (usually a B-Tree or Hash table) that stores the values of a specified column or set of columns and the locations of the corresponding rows.
When a query is executed, the database can refer to the index rather than scanning the entire table, which significantly reduces the data retrieval time.

### **2.3 Types of Indexes**

- **Primary Index:** Automatically created on the primary key of the table.
- **Unique Index:** Ensures that all values in the indexed column are unique.
- **Composite Index:** Created on multiple columns to optimize queries involving these columns.
- **Full-Text Index:** Used for full-text searches in fields containing large texts.

### **2.4 Best Practices for Indexing**

- **Create indexes on columns frequently used in the WHERE clause:** This reduces the search space when filtering data.
- **Index columns used in JOIN operations:** If your queries involve joining tables, indexing the foreign key columns can speed up these operations.
- **Avoid indexing small tables:** For small tables, scanning the entire table is often faster than using an index.
- **Limit the number of indexes:** While indexes improve read performance, they slow down write operations. Therefore, only index columns that benefit your queries.
- **Use Composite Indexes for multiple conditions:** If a query often filters or sorts by multiple columns, consider creating a composite index.

## **3. Normalization**

### **3.1 What is Normalization?**
Normalization is the process of organizing data in a database to reduce redundancy and improve data integrity. 

It involves dividing large tables into smaller, related tables and ensuring that data dependencies are properly managed.

### **3.2 Normal Forms**

Normalization is typically done through a series of steps called Normal Forms:

#### *First Normal Form (1NF):*

 Ensures that all columns contain atomic values (i.e., each column should contain indivisible values).

#### *Second Normal Form (2NF):* 

Ensures that all non-key attributes are fully functionally dependent on the primary key.
#### *Third Normal Form (3NF):*

 Ensures that there is no transitive dependency (i.e., non-key columns must not depend on other non-key columns).

### 3.3 Pros and Cons of Normalization
#### Pros:
Reduces redundancy, saving storage space.
Ensures data consistency and integrity by eliminating duplicate data.
Easier to maintain and update normalized data structures.

#### Cons:
Normalization can result in multiple joins between tables, which can slow down read performance for complex queries.
### 3.4 When to Normalize
Normalize when data consistency is a priority, especially in transactional databases where data integrity is critical.

## 4. Caching
### 4.1 What is Caching?
Caching is a mechanism for temporarily storing frequently accessed data in memory, so it can be retrieved more quickly. It reduces the need to repeatedly query the database, thus lowering the query load and improving performance.

### 4.2 Types of Caching
**In-Memory Caching:** Stores frequently accessed data in memory (RAM) using tools like Redis or Memcached. This allows faster access compared to querying the database.

**Database Query Caching:** Many databases (such as MySQL) have internal query caching mechanisms. If a query result is cached, the database can return the result without re-executing the query.

**Application-Level Caching:** The application can cache database results and store them in variables or in a dedicated cache layer to minimize database access.

**4.3 Caching Strategies**
Read-through caching: The application retrieves data from the cache, and if it's not available, it queries the database and then caches the result for future queries.

**Write-through caching:** Every time the database is updated, the cache is also updated to keep data consistent.

**Time-to-live (TTL):** Cached data is given a set expiration time to ensure that outdated data is eventually cleared from the cache.
### 4.4 Best Practices for Caching
- Cache data that changes infrequently: If data doesn’t change often, caching it can significantly improve performance.
- Set an appropriate TTL: Ensure that cached data expires after a reasonable amount of time, based on how often it changes.
- Avoid over-caching: Caching too much data can lead to excessive memory consumption and can result in cache invalidation issues.

*Indexing is a way of optimizing query performance by allowing the database to quickly locate data without scanning the entire table. Think of an index as a lookup table that helps speed up retrieval.*

### **5.1 Example: Basic Index**

Consider the following `employees` table:

| employee_id | first_name | last_name | department_id | salary  |
|-------------|------------|-----------|---------------|---------|
| 1           | John       | Doe       | 5             | 50000   |
| 2           | Jane       | Smith     | 3             | 60000   |
| 3           | Joe        | Doe       | 2             | 55000   |

Without indexing, a query that searches for employees by `last_name` would need to scan the entire table. To improve performance, you can create an index on the `last_name` column:

```sql
CREATE INDEX idx_last_name ON employees (last_name);
```

#### **Query with Index**:
```sql
SELECT * FROM employees WHERE last_name = 'Doe';
```
With this index, the database can use it to directly find rows with `last_name = 'Doe'`, avoiding a full table scan.

---

### **5.2 Example: Normalization Process**

#### **Unnormalized Table:**
| order_id | customer_name | product_name | product_quantity | product_price |
|----------|---------------|--------------|------------------|---------------|
| 1        | John Doe      | Product A    | 1                | 10.00         |
| 1        | John Doe      | Product B    | 2                | 20.00         |
| 2        | Jane Smith    | Product A    | 1                | 10.00         |

#### **After Normalization (3NF):**

- **Customers Table:**

| customer_id | customer_name |
|-------------|---------------|
| 1           | John Doe      |
| 2           | Jane Smith    |

- **Orders Table:**

| order_id | customer_id |
|----------|-------------|
| 1        | 1           |
| 2        | 2           |

- **Products Table:**

| product_id | product_name | product_price |
|------------|--------------|---------------|
| 1          | Product A    | 10.00         |
| 2          | Product B    | 20.00         |

- **Order Details Table:**

| order_id | product_id | product_quantity |
|----------|------------|------------------|
| 1        | 1          | 1                |
| 1        | 2          | 2                |
| 2        | 1          | 1                |

By splitting the data into separate tables, we eliminate redundancy and ensure that each piece of data is stored only once.

---


### **5.3 Example: Basic Caching**

Consider a query that fetches product details:

```sql
SELECT * FROM products WHERE product_id = 1;
```

Instead of querying the database every time, we can cache the result in a system like Redis.

1. **Check Cache**:
    ```bash
    GET product_1
    ```

2. **If Not Cached, Query Database**:
    ```sql
    SELECT * FROM products WHERE product_id = 1;
    ```

3. **Cache the Result**:
    ```bash
    SET product_1 "{ 'product_id': 1, 'product_name': 'Product A', 'price': 10.00 }"
    EXPIRE product_1 3600  # Cache for 1 hour
    ```

Subsequent requests can now pull data from the cache, reducing database load and improving performance.

---

## **5. Conclusion**

Understanding and implementing **Indexing**, **Normalization**, and **Caching** can significantly improve database performance. Indexing allows faster lookups, normalization ensures data integrity and reduces redundancy, and caching minimizes database load by storing frequent data in memory.

---