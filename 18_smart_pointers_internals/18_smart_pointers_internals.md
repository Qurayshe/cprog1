# Module 18: Smart Pointers & Ownership Internals

In modern C++, you almost never write `delete`. Smart pointers manage memory ownership with mathematical precision.

---

## 1. `std::unique_ptr<T>`: The Zero-Cost Pointer

A `std::unique_ptr<T>` represents **Exclusive Ownership**: exactly one pointer owns the heap memory.

- **Zero Memory Overhead:** `sizeof(std::unique_ptr<T>) == sizeof(T*)`. It is a single pointer in memory with zero extra bytes.
- **Zero Runtime Overhead:** The compiler inlines destructor calls; generated assembly is 100% identical to manual `free()`.
- **Copying is Forbidden:** You cannot copy a `unique_ptr` (would violate single ownership). You must **move** it: `auto p2 = std::move(p1);`.

---

## 2. `std::shared_ptr<T>`: Reference-Counted Ownership

A `std::shared_ptr<T>` represents **Shared Ownership**: multiple pointers can point to the same object. The object is freed when the *last* shared pointer is destroyed.

```
std::shared_ptr<T> (16 Bytes Total)
+-------------------------+---------------------------------+
| ptr: T* (8 bytes)       | control_block: ControlBlock*    |
+-------------------------+---------------------------------+
                                      |
                                      v
                          [ Control Block (Heap) ]
                          - strong_reference_count (atomic)
                          - weak_reference_count (atomic)
                          - custom deleter function
```

> [!TIP]
> **Performance Rule:** Always use `std::make_shared<T>()` instead of `std::shared_ptr<T>(new T)`. `make_shared` allocates the object and the Control Block in a **single contiguous heap allocation**, saving memory and improving CPU cache locality!

---

## Hands-On Program

Open and compile [`18_smart_pointers.cpp`](file:///c:/Users/kkhoie/Downloads/cprog1/18_smart_pointers_internals/18_smart_pointers.cpp) to see `std::unique_ptr`, `std::shared_ptr`, custom deleters, and an inspection of their physical memory sizes.
