# C & Low-Level Programming Mastery Guide

Welcome to the **C & Low-Level Programming** tutorial series. This repository is a comprehensive, hands-on curriculum spanning foundational systems programming to advanced low-level engineering concepts.

---

## 🎯 Curriculum Map

### Part 1: Foundations & Core Systems Concepts

| Module | Topic | Concepts Covered | Code Example |
| :--- | :--- | :--- | :--- |
| **[01](file:///c:/Users/kkhoie/Downloads/cprog1/01_compilation_and_memory/01_compilation_pipeline_and_data_types.md)** | **Compilation & Data Types** | Preprocessor, Compiler, Assembler, Linker, `.i`/`.s`/`.o`, Two's Complement, Wraparound | [`01_types_and_bits.c`](file:///c:/Users/kkhoie/Downloads/cprog1/01_compilation_and_memory/01_types_and_bits.c) |
| **[02](file:///c:/Users/kkhoie/Downloads/cprog1/02_memory_and_pointers/02_pointers_and_stack_memory.md)** | **Pointers & The Stack** | Address space, `&` and `*`, Pointer variables, Stack frames, Function calls, `void*` | [`02_pointer_basics.c`](file:///c:/Users/kkhoie/Downloads/cprog1/02_memory_and_pointers/02_pointer_basics.c)<br>[`02_stack_inspection.c`](file:///c:/Users/kkhoie/Downloads/cprog1/02_memory_and_pointers/02_stack_inspection.c) |
| **[03](file:///c:/Users/kkhoie/Downloads/cprog1/03_arrays_and_strings/03_arrays_strings_and_pointer_arithmetic.md)** | **Arrays & Strings** | Pointer arithmetic, Array decay, String literals, Stack vs Read-Only memory, Buffer overflows | [`03_arrays_and_strings.c`](file:///c:/Users/kkhoie/Downloads/cprog1/03_arrays_and_strings/03_arrays_and_strings.c) |
| **[04](file:///c:/Users/kkhoie/Downloads/cprog1/04_structs_and_memory_layout/04_struct_padding_alignment_and_unions.md)** | **Structs, Alignment & Unions** | Hardware word alignment, Structure padding, `offsetof`, Unions, Type punning, Bitfields | [`04_struct_alignment.c`](file:///c:/Users/kkhoie/Downloads/cprog1/04_structs_and_memory_layout/04_struct_alignment.c)<br>[`04_unions_and_bitfields.c`](file:///c:/Users/kkhoie/Downloads/cprog1/04_structs_and_memory_layout/04_unions_and_bitfields.c) |
| **[05](file:///c:/Users/kkhoie/Downloads/cprog1/05_dynamic_memory/05_dynamic_allocation_and_custom_allocators.md)** | **Dynamic Memory & Allocators** | Heap memory, `malloc`/`calloc`/`realloc`/`free`, Leaks, Use-after-free, Building an Arena Allocator | [`05_heap_memory.c`](file:///c:/Users/kkhoie/Downloads/cprog1/05_dynamic_memory/05_heap_memory.c)<br>[`05_simple_arena.c`](file:///c:/Users/kkhoie/Downloads/cprog1/05_dynamic_memory/05_simple_arena.c) |
| **[06](file:///c:/Users/kkhoie/Downloads/cprog1/06_bitwise_and_registers/06_bitwise_operations_and_hardware_registers.md)** | **Bitwise & Hardware Registers** | Bitwise operators, Bitmasks, Endianness, Emulating MMIO hardware registers | [`06_bitwise_manipulation.c`](file:///c:/Users/kkhoie/Downloads/cprog1/06_bitwise_and_registers/06_bitwise_manipulation.c)<br>[`06_endianness_and_registers.c`](file:///c:/Users/kkhoie/Downloads/cprog1/06_bitwise_and_registers/06_endianness_and_registers.c) |
| **[07](file:///c:/Users/kkhoie/Downloads/cprog1/07_function_pointers_and_vtables/07_function_pointers_and_polymorphism.md)** | **Function Pointers & OOP** | Code segment pointers, Callbacks, Jump tables, Virtual tables (vtables) in C | [`07_function_pointers_and_vtables.c`](file:///c:/Users/kkhoie/Downloads/cprog1/07_function_pointers_and_vtables/07_function_pointers_and_vtables.c) |
| **[08](file:///c:/Users/kkhoie/Downloads/cprog1/08_binary_io_and_serialization/08_binary_io_and_file_formats.md)** | **Binary I/O & File Formats** | Raw byte streams, `fread`/`fwrite`, File headers, Magic numbers, Serialization | [`08_binary_file_records.c`](file:///c:/Users/kkhoie/Downloads/cprog1/08_binary_io_and_serialization/08_binary_file_records.c) |

---

### Part 2: Advanced Low-Level & Systems Engineering

| Module | Topic | Concepts Covered | Code Example |
| :--- | :--- | :--- | :--- |
| **[09](file:///c:/Users/kkhoie/Downloads/cprog1/09_concurrency_and_atomics/09_concurrency_and_atomics.md)** | **Concurrency & Atomics** | Memory models, race conditions, atomic operations (`stdatomic.h`), spinlocks, lock-free patterns | [`09_atomics_and_spinlocks.c`](file:///c:/Users/kkhoie/Downloads/cprog1/09_concurrency_and_atomics/09_atomics_and_spinlocks.c) |
| **[10](file:///c:/Users/kkhoie/Downloads/cprog1/10_cache_and_simd/10_cache_locality_and_simd.md)** | **CPU Cache & Hardware Locality** | L1/L2/L3 cache lines (64B), spatial vs temporal locality, cache-friendly algorithms, SIMD concept | [`10_cache_locality_benchmark.c`](file:///c:/Users/kkhoie/Downloads/cprog1/10_cache_and_simd/10_cache_locality_benchmark.c) |
| **[11](file:///c:/Users/kkhoie/Downloads/cprog1/11_syscalls_and_signals/11_syscalls_and_signals.md)** | **OS System Calls & Signals** | User space vs Kernel transitions, file descriptors, asynchronous signal handling, reentrancy | [`11_signals_and_syscalls.c`](file:///c:/Users/kkhoie/Downloads/cprog1/11_syscalls_and_signals/11_signals_and_syscalls.c) |
| **[12](file:///c:/Users/kkhoie/Downloads/cprog1/12_advanced_memory_allocators/12_advanced_allocators.md)** | **Pool & Free-List Allocators** | Fixed-size memory pools, embedded free lists, $O(1)$ malloc/free with zero external fragmentation | [`12_pool_allocator.c`](file:///c:/Users/kkhoie/Downloads/cprog1/12_advanced_memory_allocators/12_pool_allocator.c) |
| **[13](file:///c:/Users/kkhoie/Downloads/cprog1/13_virtual_memory_and_mmap/13_virtual_memory_and_mmap.md)** | **Virtual Memory & Zero-Copy I/O** | Page tables, 4KB memory pages, page faults, memory-mapped files (`mmap`), zero-copy streaming | [`13_virtual_memory_pages.c`](file:///c:/Users/kkhoie/Downloads/cprog1/13_virtual_memory_and_mmap/13_virtual_memory_pages.c) |
| **[14](file:///c:/Users/kkhoie/Downloads/cprog1/14_advanced_preprocessor_metaprogramming/14_macros_and_metaprogramming.md)** | **Preprocessor Metaprogramming** | Stringification (`#`), Token pasting (`##`), Variadic macros, X-Macros for automatic code generation | [`14_xmacros_and_codegen.c`](file:///c:/Users/kkhoie/Downloads/cprog1/14_advanced_preprocessor_metaprogramming/14_xmacros_and_codegen.c) |
| **[15](file:///c:/Users/kkhoie/Downloads/cprog1/15_inline_assembly_and_intrinsics/15_inline_assembly_and_intrinsics.md)** | **Assembly & CPU Intrinsics** | Compiler intrinsics (`popcount`, `clz`), CPU cycle counter (`rdtsc`), memory barriers & volatile | [`15_intrinsics_and_cycles.c`](file:///c:/Users/kkhoie/Downloads/cprog1/15_inline_assembly_and_intrinsics/15_intrinsics_and_cycles.c) |
| **[16](file:///c:/Users/kkhoie/Downloads/cprog1/16_bytecode_vm_interpreter/16_bytecode_vm.md)** | **Building a Bytecode Virtual Machine** | Instruction set design (ISA), fetch-decode-execute loop, stack VM, opcodes, bytecode compilation | [`16_stack_vm.c`](file:///c:/Users/kkhoie/Downloads/cprog1/16_bytecode_vm_interpreter/16_stack_vm.c) |

---

## 🧠 The Low-Level Mental Model

When your C program runs, the operating system gives it a **Virtual Address Space**:

```
High Addresses (0xFFFFFFFF... on 64-bit)
+-------------------------------------------------------+
| Kernel Space (Accessible only in supervisor mode)     |
+-------------------------------------------------------+
| Stack (Grows DOWNWARD on x86/x64/ARM)                 |
|   - Function local variables                          |
|   - Return addresses & stack frame metadata           |
|                           |                           |
|                           v                           |
|                                                       |
|                           ^                           |
|                           |                           |
| Heap (Grows UPWARD)                                   |
|   - Dynamic memory requested via malloc()             |
+-------------------------------------------------------+
| BSS Segment (Block Started by Symbol)                 |
|   - Uninitialized global & static variables (= 0)     |
+-------------------------------------------------------+
| Data Segment                                          |
|   - Initialized global & static variables             |
+-------------------------------------------------------+
| Text / Code Segment (Read-Only & Executable)          |
|   - Compiled machine instructions                     |
|   - String literals ("Hello World")                   |
+-------------------------------------------------------+
Low Addresses (0x00000000...)
```

---

## 🛠️ How to Compile and Run

Every example in this course is standard C (C99/C11) with zero external dependencies.

```bash
# Standard compilation with full warnings
gcc -Wall -Wextra -std=c11 <module_folder>/<source_file>.c -o <output_binary>
./<output_binary>
```
