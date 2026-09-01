# Module 21: Non-Owning Memory Views (`std::span` & `std::string_view`)

In C, passing a sub-array or sub-string required either allocating a new buffer or passing two variables (`char* ptr, size_t len`) everywhere.

Modern C++ provides **Non-Owning Views**: lightweight pairs of `[Pointer, Length]` that reference existing memory with **zero heap allocations and zero memory copies**.

---

## 1. `std::string_view` (C++17)

A `std::string_view` is just a pointer and a length: `struct { const char* data; size_t size; }` (16 bytes).

```cpp
void parse_header(std::string_view str) {
    // Slicing is O(1) instantaneous:
    std::string_view prefix = str.substr(0, 4); // No malloc, no copy!
}
```

---

## 2. `std::span<T>` (C++20)

`std::span<T>` is a view over contiguous elements of any type (raw C array, `std::vector`, `std::array`).

```cpp
void process_samples(std::span<const float> samples) {
    for (float s : samples) { ... }
}

float c_arr[100];
std::vector<float> vec(100);

process_samples(c_arr); // Works on raw C arrays!
process_samples(vec);   // Works on std::vector!
process_samples({vec.data() + 10, 20}); // Works on sub-slices!
```

> [!CAUTION]
> **Dangling View Hazard:** Views do not own the memory. If the underlying vector or string is destroyed, the view becomes a dangling pointer!

---

## 🔬 Hands-On Program

Open and compile [`21_memory_views.cpp`](file:///c:/Users/kkhoie/Downloads/cprog1/21_views_and_zero_copy/21_memory_views.cpp) to see zero-copy sub-slicing across raw arrays and vectors.
