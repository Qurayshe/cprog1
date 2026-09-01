# Module 15: CPU Intrinsics & Cycle Counting

Sometimes, high-level C abstractions cannot access dedicated silicon instructions built into modern CPUs (like population count, leading-zero count, or hardware timestamp counters).

**Compiler Intrinsics** allow you to emit these single-cycle hardware instructions directly from C without writing standalone assembly files.

---

## 1. Common Hardware Intrinsics (GCC / Clang / MSVC)

| Function | Hardware Instruction | Description |
| :--- | :--- | :--- |
| `__builtin_popcount(x)` | `POPCNT` (x86) / `CNT` (ARM) | Counts the number of set bits (1s) in a single CPU cycle. |
| `__builtin_clz(x)` | `LZCNT` / `BSR` | Counts leading zeros (useful for fast log2 and bit allocation). |
| `__builtin_ctz(x)` | `TZCNT` / `BSF` | Counts trailing zeros (finds index of lowest set bit). |
| `__builtin_bswap32(x)` | `BSWAP` | Reverses byte order in 1 CPU cycle. |

---

## 2. Hardware Timestamp Counter (Cycle Counting)

On x86/x64 CPUs, the `RDTSC` (Read Time-Stamp Counter) instruction reads the exact number of CPU clock cycles elapsed since the processor was powered on:

```c
#if defined(__x86_64__) || defined(_M_X64)
#include <x86intrin.h>
uint64_t start_cycles = __rdtsc();
// ... execute hot code loop ...
uint64_t elapsed = __rdtsc() - start_cycles;
#endif
```
This enables nano-benchmark profiling down to single-cycle precision!

---

## 3. Compiler Optimization Barriers

When writing micro-benchmarks or device drivers, the compiler optimizer might reorder or eliminate variables. A **Memory Barrier** tells the compiler: *"Do not reorder memory reads/writes across this point!"*

```c
// GCC/Clang optimization barrier:
#define COMPILER_BARRIER() asm volatile("" ::: "memory")
```

---

## 🔬 Hands-On Program

Open and compile [`15_intrinsics_and_cycles.c`](file:///c:/Users/kkhoie/Downloads/cprog1/15_inline_assembly_and_intrinsics/15_intrinsics_and_cycles.c) to inspect bit counting intrinsics and cycle-accurate measurement techniques.
