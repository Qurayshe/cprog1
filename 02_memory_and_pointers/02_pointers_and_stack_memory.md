# Module 02: Pointers & The Call Stack

Pointers are often considered the hardest part of C, but they are conceptually simple once you visualize physical memory.

---

## 1. What is Memory?

Think of your computer's RAM as an enormous array of contiguous 1-byte storage lockers.
Each locker has a unique numerical label: its **Memory Address**.

```
Address (Hex):    0x1000    0x1001    0x1002    0x1003    0x1004
               +---------+---------+---------+---------+---------+
Value (Bytes): |  0x42   |  0x00   |  0x1A   |  0xFF   |  0x05   |
               +---------+---------+---------+---------+---------+
```

---

## 2. What is a Pointer?

A **Pointer** is simply a variable whose stored value is a **Memory Address** of another variable.

```c
int x = 42;      // Allocated at some memory address, e.g., 0x7FFF0010
int *p = &x;     // 'p' holds the number 0x7FFF0010
```

```
Variable:      p (Pointer)                x (Integer)
Address:     0x7FFF0000                 0x7FFF0010
Value:     [ 0x7FFF0010 ] ------------> [    42     ]
```

### The Two Fundamental Operators:
1. **Address-Of (`&`)**: "Where does this variable live?"
   - `&x` yields the memory address of `x`.
2. **Dereference (`*`)**: "What value lives at this address?"
   - `*p` reads or writes the memory location pointed to by `p`.

---

## 3. Pointer Types & `void*`

Why do pointers need types if all 64-bit addresses are just 8-byte numbers?
1. **Dereferencing Width**: When you do `*p`, the compiler needs to know how many bytes to read/write (1 byte for `char*`, 4 bytes for `int*`, 8 bytes for `double*`).
2. **Pointer Arithmetic**: `p + 1` advances the address by `sizeof(*p)` bytes, not necessarily 1 byte.

### The `void*` (Generic Pointer):
A `void*` can hold any memory address, but **cannot be dereferenced directly** because its width is unknown. You must cast it to a concrete type before dereferencing:
```c
void *generic = &x;
int value = *(int*)generic; // Cast required
```

---

## 4. The Stack & Function Call Frames

The **Stack** is managed automatically by the CPU and compiler.

Whenever a function is called:
1. A new **Stack Frame** (or Activation Record) is pushed onto the stack.
2. The frame holds:
   - Function arguments
   - Local variables
   - The Return Address (where to resume execution when the function exits)
3. When the function returns, its frame is popped (the stack pointer `RSP`/`ESP` moves back).

```
High Memory
+------------------------------------+
| main() stack frame                 |
|   - int main_var                   |
+------------------------------------+
| calculate() stack frame            | <-- Function called
|   - int arg1, arg2                 |
|   - int local_result               |
|   - Return address to main()       |
+------------------------------------+ <-- Stack Pointer (RSP)
| (Available unused stack space)     |
v Grows Downward                     |
Low Memory
```

> [!CAUTION]
> **The Classic Dangling Stack Pointer Bug:**
> Never return a pointer to a local variable!
> ```c
> int* bad_function(void) {
>     int temp = 100;
>     return &temp; // DANGER: 'temp' memory is invalidated immediately upon return!
> }
> ```

---

## Hands-On Programs

1. [`02_pointer_basics.c`](file:///c:/Users/kkhoie/Downloads/cprog1/02_memory_and_pointers/02_pointer_basics.c): Addresses, dereferencing, pass-by-reference, and swap functions.
2. [`02_stack_inspection.c`](file:///c:/Users/kkhoie/Downloads/cprog1/02_memory_and_pointers/02_stack_inspection.c): Live inspection of stack growth, variable addresses across nested function calls, and stack frame lifetime.
