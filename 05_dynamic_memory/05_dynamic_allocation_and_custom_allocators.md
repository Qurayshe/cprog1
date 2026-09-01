# Module 05: Dynamic Memory Allocation & Custom Allocators

While the Stack is fast and automatically managed, its size is fixed at program startup and variables disappear when functions return. The **Heap** provides dynamically sized, persistent memory across function lifecycles.

---

## 1. The Standard Dynamic Memory Functions

Included from `<stdlib.h>`:

| Function | Signature | Description |
| :--- | :--- | :--- |
| `malloc` | `void* malloc(size_t size)` | Allocates `size` uninitialized bytes. Returns `NULL` on failure. |
| `calloc` | `void* calloc(size_t num, size_t size)` | Allocates and zeroes out `num * size` bytes. |
| `realloc`| `void* realloc(void *ptr, size_t new_size)` | Resizes an existing block (may move to a new memory address!). |
| `free`   | `void free(void *ptr)` | Returns memory to the OS heap manager. Passing `NULL` is a safe no-op. |

---

## 2. Common Low-Level Memory Bugs

1. **Memory Leak**: Allocating memory with `malloc` and losing the pointer before calling `free`.
2. **Dangling Pointer**: A pointer holding the address of memory that has already been `free`'d.
3. **Use-After-Free (UAF)**: Dereferencing a dangling pointer. This is one of the most common causes of critical security vulnerabilities.
4. **Double Free**: Calling `free(ptr)` twice on the same address. Causes heap corruption.
   > **Defensive Rule:** Always set pointers to `NULL` after freeing:
   > ```c
   > free(ptr);
   > ptr = NULL; // Safe!
   > ```

---

## 3. The Power of Arena Allocators (Bump Allocators)

Calling `malloc` thousands of times per second causes:
- System call overhead.
- Heap fragmentation (scattered small holes in memory).
- Cache misses.

High-performance systems (game engines like Unreal/Unity, compilers, web servers) often use **Arena Allocators**:
- Pre-allocate one large contiguous chunk of memory (e.g. 1 MB).
- Allocate by simply incrementing an offset pointer (**"bump" allocation** - essentially $O(1)$ fast).
- Free **everything at once** in $O(1)$ time by resetting the offset to 0.

```
Arena Memory Block (e.g., 64 KB Buffer)
+-------------------+--------------------+--------------------------------+
| Allocation 1 (8B) | Allocation 2 (64B) | Free Space                     |
+-------------------+--------------------+--------------------------------+
0                   8                    72                               65536
                                         ^
                                         |
                                    arena.offset (Bumps forward)
```

---

## Hands-On Programs

1. [`05_heap_memory.c`](file:///c:/Users/kkhoie/Downloads/cprog1/05_dynamic_memory/05_heap_memory.c): Dynamic array implementation with resizing (`realloc`), defensive error checking, and cleanup.
2. [`05_simple_arena.c`](file:///c:/Users/kkhoie/Downloads/cprog1/05_dynamic_memory/05_simple_arena.c): A complete, working, zero-fragmentation Arena Allocator in pure C.
