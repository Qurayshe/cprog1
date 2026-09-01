# Module 19: Move Semantics & Rvalue References (`&&`)

Before C++11, passing or returning large objects (like a 10 MB buffer or dynamic vector) forced an expensive deep copy:
`malloc` a second 10 MB chunk, `memcpy` all bytes, and `free` the original.

**Move Semantics** allows you to "steal" heap pointers from temporary objects in $O(1)$ time.

---

## 1. Lvalues vs. Rvalues

- **Lvalue (Left-value):** An expression that has an identifiable memory address in RAM (e.g. named variables: `int x = 5;`).
- **Rvalue (Right-value):** A temporary, transient value that does not persist beyond the single expression (e.g. `5 + 10`, or a temporary returned by value from a function).
- **Rvalue Reference (`Type&&`):** A reference that can bind specifically to temporary objects to enable resource stealing.

---

## 2. Copy vs. Move Mechanics

Suppose `Buffer` has an internal pointer `int* data; size_t size;`.

### Deep Copy Constructor ($O(N)$):
```cpp
Buffer(const Buffer& other) {
    size = other.size;
    data = new int[size];
    std::memcpy(data, other.data, size * sizeof(int)); // Expensive!
}
```

### Move Constructor ($O(1)$ Pointer Steal):
```cpp
Buffer(Buffer&& other) noexcept {
    data = other.data;       // 1. Steal the heap pointer!
    size = other.size;
    other.data = nullptr;    // 2. Nullify source so its destructor won't free it!
    other.size = 0;
}
```

---

## 3. What `std::move()` Really Does

`std::move(x)` does **not move anything at runtime**.
It is simply a compile-time cast that transforms an lvalue `x` into an rvalue reference `x&&`, telling the compiler: *"I no longer need this object; feel free to steal its resources!"*

---

## Hands-On Program

Open and compile [`19_move_semantics.cpp`](file:///c:/Users/kkhoie/Downloads/cprog1/19_move_semantics_and_rvalues/19_move_semantics.cpp) to see live tracking of deep copies vs instant move pointer transfers.
