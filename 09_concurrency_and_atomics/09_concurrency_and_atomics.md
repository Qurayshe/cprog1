# Module 09: Concurrency, Threading & Atomic Operations

When multiple CPU cores execute instructions simultaneously, operations that seem "simple" in C (like `counter++`) are **not atomic** and cause dangerous **Race Conditions**.

---

## 1. Why `counter++` is Dangerous in Multithreading

At the assembly level, `counter++` is actually **3 distinct CPU instructions**:
1. `MOV eax, [counter]`  (Read current value from RAM/cache into CPU register)
2. `INC eax`             (Increment value in CPU register)
3. `MOV [counter], eax`  (Write updated value back to RAM/cache)

```
Thread A: [Read 10] -------> [Inc to 11] --------------> [Write 11]
Thread B:        [Read 10] -----------> [Inc to 11] --------------> [Write 11] (LOST UPDATE!)
```
Both threads incremented, but the final value in memory is 11 instead of 12!

---

## 2. C11 Atomics (`<stdatomic.h>`)

C11 introduced standard atomic types and memory order operations. An **atomic operation** executes as a single, indivisible hardware transaction at the CPU level using bus locking or cache-coherence protocols (`LOCK CMPXCHG` on x86).

### Key Functions:
- `atomic_int counter;`
- `atomic_fetch_add(&counter, 1);`
- `atomic_load(&counter);`
- `atomic_store(&counter, val);`
- `atomic_compare_exchange_strong(&counter, &expected, desired);` (CAS - Compare-And-Swap)

---

## 3. Building a Spinlock from Scratch with Compare-And-Swap (CAS)

A **Spinlock** is a low-level synchronization primitive that repeatedly polls in a tight loop until a lock becomes available:

```c
typedef atomic_bool spinlock_t;

void spinlock_lock(spinlock_t *lock) {
    // Repeatedly try to change false -> true
    while (atomic_exchange(lock, true)) {
        // CPU yield or pause instruction
    }
}

void spinlock_unlock(spinlock_t *lock) {
    atomic_store(lock, false);
}
```

---

## 🔬 Hands-On Program

Compile and run [`09_atomics_and_spinlocks.c`](file:///c:/Users/kkhoie/Downloads/cprog1/09_concurrency_and_atomics/09_atomics_and_spinlocks.c) to see compare-and-swap operations, lock-free atomic counters, and custom spinlock mechanics.
