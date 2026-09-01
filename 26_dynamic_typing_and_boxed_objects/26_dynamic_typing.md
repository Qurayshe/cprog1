# Module 26: Dynamic Typing & `PyObject` (Working Backwards)

In Python or JavaScript, you can reassign any variable to any type at runtime:
```python
x = 42       # Integer
x = "hello"  # String
x = [1, 2]   # List
```
How does a processor (which only understands rigid fixed-width 32/64-bit integer registers) execute this?

---

## 1. High-Level Concept: Dynamic Typing & Boxing

In dynamic languages, variables **do not hold raw primitive numbers**.
Instead, variables hold a pointer to a **Boxed Object** on the heap.

```
Variable 'x' (Pointer) ---> [ Heap Object: PyObject / JSValue ]
                            - Type Tag (Integer, String, Array)
                            - Reference Count
                            - Payload Data
```

---

## 2. Low-Level Reality: Tagged Unions & CPython `PyObject`

Under the hood in CPython (written in pure C), every single Python object starts with the exact same C struct header:

```c
typedef struct _object {
    size_t ob_refcnt;          // Reference counter (Module 18)
    struct _typeobject *ob_type; // Pointer to Type Descriptor / VTable (Module 07)
} PyObject;
```

A Python integer (`PyLongObject`) is simply:
```c
typedef struct {
    PyObject ob_base; // Inherits header
    int64_t ob_digit; // The actual integer
} PyLongObject;
```

### The Cost of Dynamic Typing:
1. **Memory Overhead:** Storing `42` takes 4 bytes in C, but **28 to 32 bytes** in Python due to the object header!
2. **Pointer Indirection:** Doing `x + y` requires dereferencing two heap pointers, inspecting their type tags, branching to the integer addition function, and allocating a *new* boxed object for the result.

---

## Hands-On Program

Open and compile [`26_boxed_types_and_tagging.c`](file:///c:/Users/kkhoie/Downloads/cprog1/26_dynamic_typing_and_boxed_objects/26_boxed_types_and_tagging.c) to see dynamic type resolution, tagged unions, and dynamic dispatch in C.
