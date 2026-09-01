# Module 12: Fixed-Size Pool & Free-List Allocators

In Module 05, we built an **Arena Allocator**, which is great when objects share a single lifetime. But what if you need to dynamically create and destroy individual objects in arbitrary order without causing heap fragmentation?

This is where **Fixed-Size Pool Allocators** excel.

---

## 1. How a Fixed-Size Block Pool Works

A Pool Allocator breaks a large memory buffer into chunks of identical size (e.g. 64 bytes each).

Instead of storing external bookkeeping metadata, it uses the **unallocated memory chunks themselves** to store a linked list of free blocks! This is called an **Embedded Free-List**.

```
Pool Buffer with 4 Slots (e.g., 64 bytes each):

Initially Free (Free List points to Slot 0):
[ Next -> Slot 1 ]   [ Next -> Slot 2 ]   [ Next -> Slot 3 ]   [ Next -> NULL ]
     Slot 0               Slot 1               Slot 2               Slot 3
       ^
       |
  pool.free_list

After Allocating 1 Object (Returns Slot 0 to caller):
[ USER DATA ...  ]   [ Next -> Slot 2 ]   [ Next -> Slot 3 ]   [ Next -> NULL ]
     Slot 0               Slot 1               Slot 2               Slot 3
                          ^
                          |
                     pool.free_list
```

---

## 2. Advantages of Pool Allocators

1. **Deterministic $O(1)$ Allocation & Free:** Allocating is just `free_list = free_list->next`. Freeing is `ptr->next = free_list; free_list = ptr;`.
2. **Zero External Memory Fragmentation:** Because every block is identical in size, holes can always be filled by any subsequent allocation.
3. **Cache Friendly:** All objects live contiguous in physical RAM.

---

## Hands-On Program

Compile and run [`12_pool_allocator.c`](file:///c:/Users/kkhoie/Downloads/cprog1/12_advanced_memory_allocators/12_pool_allocator.c) to see a complete, industrial-grade implementation of a Fixed-Size Memory Pool.
