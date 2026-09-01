# Module 29: Databases & B-Tree Indexes (Working Backwards)

When you run `SELECT * FROM users WHERE user_id = 48192;` in PostgreSQL, MySQL, or SQLite, the database queries through 10,000,000 rows in **0.1 milliseconds**. Why doesn't it scan the whole file?

---

## 1. High-Level Concept: The SQL Database Index

```sql
CREATE INDEX idx_user_id ON users(user_id);
-- Now searches are O(log N) instead of O(N) sequential table scan!
```

---

## 2. Low-Level Reality: 4 KB Page B-Trees on Disk

Databases do not store binary search trees with 2 children per node.
Why? Because every pointer hop in a binary search tree would require a separate disk read!

Instead, databases use **B-Trees / B+ Trees** where **every node is formatted to fit exactly inside a 4,096-byte (4 KB) OS Virtual Memory Page** (Module 13)!

```
[ Root B-Tree Node (Exact 4 KB Disk Page) ]
Keys:     [ 1000 | 5000 | 9000 ]
Offsets:  /      |      |      \
         v       v      v       v
      [ Page 1] [Page 2] [Page 3] [Page 4] (Each 4 KB)
```

### Why 4 KB B-Tree Nodes are Ultra-Fast:
1. **Single Disk I/O:** Reading 1 page loads hundreds of keys into L1/L2 cache in a single read.
2. **Shallow Depth:** A 3-level 4 KB B-Tree can index **over 10,000,000 records** in just **3 disk page reads**!
3. **Binary File Offsets:** The leaves store byte offsets pointing directly to binary records in the main database file (Module 08).

---

## 🔬 Hands-On Program

Open and compile [`29_page_btree_indexer.c`](file:///c:/Users/kkhoie/Downloads/cprog1/29_database_indexes_and_btrees/29_page_btree_indexer.c) to see a page-based index search engine in pure C.
