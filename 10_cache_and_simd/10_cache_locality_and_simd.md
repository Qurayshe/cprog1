# Module 10: CPU Cache Locality & Hardware Performance

The modern CPU is blazing fast (~3-5 GHz), but fetching data from main memory (RAM) is relatively very slow (~100-200 CPU cycles latency).

To hide this latency, CPUs use a multi-tiered hierarchy of high-speed static RAM caches:

```
[ CPU Core Registers (1 cycle latency) ]
                  |
         [ L1 Cache (~32-64 KB, ~4 cycles) ]
                  |
         [ L2 Cache (~256-512 KB, ~12 cycles) ]
                  |
         [ L3 Shared Cache (~8-64 MB, ~40 cycles) ]
                  |
         [ Main Memory RAM (~16-64 GB, ~200 cycles) ]
```

---

## 1. The 64-Byte Cache Line

CPUs **never read a single byte from RAM**.
Whenever memory is requested, the hardware fetches an entire **64-byte Cache Line** into L1 cache.

```
Request: &arr[0] (4 bytes)
CPU loads: [ arr[0] | arr[1] | arr[2] | ... | arr[15] ] (64 Bytes total!)
```
When your program immediately reads `arr[1]`, it is already sitting in L1 cache: this is an instantaneous **Cache Hit**!

---

## 2. Spatial vs. Temporal Locality

- **Spatial Locality:** Accessing memory addresses physically close to recent accesses (e.g. iterating linearly through a flat array).
- **Temporal Locality:** Accessing the same memory address repeatedly within a short window of time (e.g. a loop counter variable).

---

## 3. The Classic Matrix Traversal: Row-Major vs Column-Major

C stores 2D arrays in **Row-Major Order** (row 0, then row 1, then row 2...).

### Cache-Friendly (Row-by-Row):
```c
// Stride-1 access: Sequential, uses all 16 integers in every 64-byte cache line!
for (int r = 0; r < ROWS; r++) {
    for (int c = 0; c < COLS; c++) {
        sum += matrix[r][c];
    }
}
```

### Cache-Disastrous (Column-by-Column):
```c
// Stride-N access: Jumps thousands of bytes per iteration, causing a CACHE MISS on EVERY read!
for (int c = 0; c < COLS; c++) {
    for (int r = 0; r < ROWS; r++) {
        sum += matrix[r][c];
    }
}
```
Row-major traversal is often **5x to 20x faster** on modern hardware simply because of cache utilization!

---

## 🔬 Hands-On Program

Open and compile [`10_cache_locality_benchmark.c`](file:///c:/Users/kkhoie/Downloads/cprog1/10_cache_and_simd/10_cache_locality_benchmark.c) to run a live micro-benchmark measuring the execution time differences between cache-friendly and cache-thrashing access patterns.
