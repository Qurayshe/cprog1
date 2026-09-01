# Module 25: Garbage Collection Under the Hood (Working Backwards)

In Python, Java, Go, and JavaScript, you never call `free()`. Memory disappears automatically when you stop using it. How does the runtime engine know which memory is safe to destroy?

---

## 1. High-Level Concept: Automatic Garbage Collection

In high-level languages:
```javascript
function createUser() {
    let user = { name: "Alice", id: 42 }; // Allocated on heap
    return user.id;
} // 'user' is no longer reachable... GC reclaims it!
```

---

## 2. Low-Level Reality: Mark-and-Sweep Garbage Collection

To clean up memory without programmer intervention, a **Tracing Garbage Collector** executes two distinct low-level phases:

### Phase 1: The "Mark" Phase (Graph Traversal from Stack Roots)
1. The GC looks at the active **Stack Frame** (Module 02). Any local variable containing a pointer is declared a **Root**.
2. Starting from the Roots, the GC recursively traverses every pointer field inside heap structs (Graph Depth-First Search).
3. Every visited object has a single header bit set: `obj->is_marked = 1;`.

```
[ Active Stack Frame (Roots) ]
         |
         v
    [ Object A (marked=1) ] ---> [ Object B (marked=1) ]
                                          |
                                          x  [ Object C (UNREACHABLE / marked=0) ]
```

### Phase 2: The "Sweep" Phase (Linear Heap Cleanup)
1. The GC iterates linearly through the entire allocated heap memory list (Module 05).
2. If `obj->is_marked == 1`, the GC resets it (`is_marked = 0`) for the next cycle.
3. If `obj->is_marked == 0`, **no live stack variable can reach this object** $\to$ the GC calls `free(obj)`!

> [!IMPORTANT]
> **Why "Stop-The-World" Pauses Happen in Java/Go:**
> While the GC traverses the pointer graph, if another thread modifies a pointer, the GC might accidentally miss a live object and free it! Therefore, the runtime must temporarily freeze all application threads.

---

## 🔬 Hands-On Program

Open and compile [`25_mark_and_sweep_gc.c`](file:///c:/Users/kkhoie/Downloads/cprog1/25_garbage_collection_internals/25_mark_and_sweep_gc.c) to see a complete Mark-and-Sweep Garbage Collector implemented from scratch in pure C.
