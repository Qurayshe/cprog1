# Module 03: Arrays, Strings & Pointer Arithmetic

In C, arrays and strings are not high-level dynamic objects—they are raw, contiguous memory buffers.

---

## 1. The Big Secret: Array Decay

In almost all expressions, an array variable automatically **decays** into a pointer to its first element:
```c
int numbers[5] = {10, 20, 30, 40, 50};
int *ptr = numbers; // 'numbers' decays to &numbers[0]
```

### Array Indexing is Just Pointer Arithmetic!
The expression `numbers[i]` is identical to `*(numbers + i)`:
```c
numbers[2]    <===>    *(numbers + 2)    <===>    2[numbers] // Valid C syntax!
```

---

## 2. Pointer Arithmetic Step Size

When you add an integer $k$ to a pointer `p`:
$$\text{New Address} = \text{Address}(p) + (k \times \text{sizeof}(*p))$$

```
int array (sizeof(int) = 4):
Address:    0x1000       0x1004       0x1008       0x100C
         +------------+------------+------------+------------+
Elements:|  arr[0]=10 |  arr[1]=20 |  arr[2]=30 |  arr[3]=40 |
         +------------+------------+------------+------------+
              ^            ^
              |            |
             ptr        ptr + 1  (Address advanced by +4 bytes)
```

---

## 3. C Strings: The Null Terminator (`\0`)

A string in C is merely a contiguous array of `char` bytes ending with the byte `0` (the ASCII `NUL` character, written as `'\0'`).

```
String: "HELLO" (Length 5, requires 6 bytes in memory)
Memory: [ 'H' | 'E' | 'L' | 'L' | 'O' | '\0' ]
ASCII:  [ 72  | 69  | 76  | 76  | 79  |  0   ]
```

> [!IMPORTANT]
> **Char Array vs String Literal Memory Placement:**
> - `char stack_str[] = "Hello";` allocates 6 bytes on the **Stack**. You can modify characters: `stack_str[0] = 'h';` (OK).
> - `const char *ro_str = "Hello";` points to the **Read-Only Data (Text) Segment**. Attempting `ro_str[0] = 'h';` causes a **Segmentation Fault / Access Violation Crash**!

---

## 4. Multi-Dimensional Arrays in Memory

A 2D array in C (e.g. `int matrix[3][4]`) is laid out in **Row-Major Order** (one continuous flat row after another):

```
matrix[0][0], matrix[0][1], matrix[0][2], matrix[0][3], matrix[1][0], ...
```

---

## Hands-On Program

Compile and run [`03_arrays_and_strings.c`](file:///c:/Users/kkhoie/Downloads/cprog1/03_arrays_and_strings/03_arrays_and_strings.c) to see array indexing mechanics, pointer step sizes across different types, and how to write custom C string utilities without `<string.h>`.
