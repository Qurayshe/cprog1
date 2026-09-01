# Module 20: Compile-Time Metaprogramming (`constexpr` & `consteval`)

Why compute values when your program is running on a user's CPU if you can compute them **once inside the compiler** at build time?

---

## 1. The `constexpr` and `consteval` Keywords

- **`constexpr`:** Indicates that a function or variable *can* be evaluated at compile time if its inputs are known constants, but can also run at runtime if given dynamic inputs.
- **`consteval` (C++20):** Guarantees that the function **MUST** be evaluated at compile time. Attempting to pass runtime variables causes a compiler error.

```cpp
constexpr uint32_t compile_time_hash(const char* str) {
    uint32_t hash = 5381;
    while (*str) {
        hash = ((hash << 5) + hash) + (uint8_t)(*str++);
    }
    return hash;
}

// Emits an immediate constant integer directly into the assembly opcodes:
constexpr uint32_t msg_hash = compile_time_hash("GET /index.html");
```

---

## 2. Compile-Time Lookup Tables

In game development, audio processing, and cryptography, lookup tables (LUTs) are used to avoid runtime math calculations. With `constexpr`, you can generate these tables at compile time with zero runtime startup penalty!

---

## Hands-On Program

Open and compile [`20_constexpr_demo.cpp`](file:///c:/Users/kkhoie/Downloads/cprog1/20_constexpr_and_compile_time/20_constexpr_demo.cpp) to see compile-time string hashing and precomputed trigonometric lookup tables.
