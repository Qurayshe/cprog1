# Module 23: Cache Alignment & False Sharing

In multi-threaded systems, **False Sharing** is one of the most subtle and devastating performance bugs.

---

## 1. What is False Sharing?

Remember from Module 10: CPUs fetch data in **64-byte Cache Lines**.

If Thread 1 (Core 1) writes to `data.a`, and Thread 2 (Core 2) writes to `data.b`, and both variables happen to sit in the **same 64-byte cache line**:
- Whenever Core 1 writes, the hardware **Cache Coherence Protocol (MESI)** invalidates the cache line on Core 2!
- Whenever Core 2 writes, it invalidates the cache line on Core 1!
- The two CPU cores spend all their time bouncing the cache line across the silicon interconnect instead of doing useful work.

```
[ Thread 1 (Core 1) ]                            [ Thread 2 (Core 2) ]
        |                                                 |
        +------> [ 64-Byte Shared Cache Line: { a, b } ] <+
                   (Bounces back and forth in cache ping-pong!)
```

---

## 2. The Solution: `alignas(64)`

Modern C++ provides the `alignas` specifier to force variables onto separate 64-byte cache lines:

```cpp
struct PaddedData {
    alignas(64) uint64_t thread_1_counter; // Offset 0..63
    alignas(64) uint64_t thread_2_counter; // Offset 64..127
};
```
Now, Core 1 and Core 2 operate on completely independent hardware cache lines with **zero interference**!

---

## 🔬 Hands-On Program

Open and compile [`23_false_sharing_benchmark.cpp`](file:///c:/Users/kkhoie/Downloads/cprog1/23_cache_alignment_and_false_sharing/23_false_sharing_benchmark.cpp) to see how `alignas(64)` eliminates false sharing across concurrent threads.
