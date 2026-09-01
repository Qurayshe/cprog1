# Module 22: Polymorphic Memory Resources (`std::pmr`)

In Module 05, we built a custom C Arena Allocator. In C++17, the standard library added **PMR (Polymorphic Memory Resources)**, allowing standard containers (`std::vector`, `std::string`, `std::map`) to allocate from **Stack-backed arenas, bump allocators, or memory pools**!

---

## 1. Why Standard `std::vector` is Inflexible

A standard `std::vector<int>` hardcodes heap `new`/`delete` calls.
With PMR, `std::pmr::vector<int>` delegates all allocations to an underlying `std::pmr::memory_resource*`.

---

## 2. Stack-Backed Monotonic Buffers

You can create an `std::pmr::vector` that uses a pre-allocated stack buffer:

```cpp
#include <memory_resource>
#include <vector>

std::array<std::byte, 1024> stack_buf;
std::pmr::monotonic_buffer_resource pool(stack_buf.data(), stack_buf.size());

// This vector allocates ZERO heap memory!
std::pmr::vector<int> vec(&pool);
vec.push_back(42); // Allocated directly inside 'stack_buf'!
```

---

## 🔬 Hands-On Program

Open and compile [`22_pmr_arena.cpp`](file:///c:/Users/kkhoie/Downloads/cprog1/22_pmr_and_custom_allocators/22_pmr_arena.cpp) to see custom memory resources and zero-heap `std::pmr` vector allocations.
