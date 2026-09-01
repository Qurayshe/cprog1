# Module 17: RAII & Deterministic Resource Management

In C, every resource you acquire (`malloc`, `fopen`, `socket`, `pthread_mutex_lock`) requires manual cleanup. If a function returns early on error, memory leaks and deadlocks occur.

**RAII (Resource Acquisition Is Initialization)** is the foundational idiom of modern C++ systems programming.

---

## 1. The Core RAII Principle

1. **Acquire in Constructor:** Acquire the resource when the object is initialized.
2. **Release in Destructor:** Release the resource inside `~Class()`.
3. **Bind to Stack Scope:** When the object leaves its stack scope (via normal return, `break`, or exception), the CPU **guarantees** the destructor will execute automatically.

```cpp
void process_file() {
    FileHandle file("data.bin", "rb"); // Acquired
    if (!file.is_valid()) return;      // Destructor called automatically!

    // ... do work ...
} // Destructor called automatically here!
```

---

## 2. RAII Mutex Locking: `std::lock_guard`

In C, releasing a mutex requires calling `unlock` before every single `return` path.
In C++, `std::lock_guard` locks upon creation and unlocks when going out of scope:

```cpp
std::mutex mtx;

void thread_safe_work() {
    std::lock_guard<std::mutex> lock(mtx); // Mutex acquired
    // Critical section
    if (error_condition) return; // Mutex safely released automatically!
}
```

---

## Hands-On Program

Open and compile [`17_raii_demo.cpp`](file:///c:/Users/kkhoie/Downloads/cprog1/17_raii_and_resources/17_raii_demo.cpp) to see custom RAII wrappers for raw file pointers and scoped memory buffers.
