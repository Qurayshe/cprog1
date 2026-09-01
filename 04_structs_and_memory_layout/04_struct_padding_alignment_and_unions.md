# Module 04: Structs, Memory Alignment, Padding & Unions

When you group variables inside a `struct`, the compiler does not simply pack them side-by-side without gaps. Understanding **Memory Alignment** is essential for high-performance computing, network protocols, and driver development.

---

## 1. Hardware Memory Alignment & Padding

Modern CPUs fetch memory in 32-bit (4-byte) or 64-bit (8-byte) chunks known as **Memory Words**.
- A 4-byte integer is fastest to read when its address is a multiple of 4 ($4N$).
- An 8-byte integer/pointer is fastest when its address is a multiple of 8 ($8N$).
- If data is unaligned, the CPU may have to perform two memory accesses and stitch the bytes together, causing a significant performance penalty (or a hardware bus fault on ARM).

### The Padding Mystery:
Consider this struct:
```c
struct BadLayout {
    char a;      // 1 byte
    int b;       // 4 bytes
    char c;      // 1 byte
};
```
You might expect `sizeof(struct BadLayout)` to be $1 + 4 + 1 = 6$ bytes.
**In reality, it is 12 bytes!**

```
Memory Layout of struct BadLayout (12 Bytes Total):
+--------+-----------------+----------------+--------+-----------------+
| a (1B) | [Pad: 3 Bytes]  |    b (4B)      | c (1B) | [Pad: 3 Bytes]  |
+--------+-----------------+----------------+--------+-----------------+
Byte 0    Byte 1..3         Byte 4..7        Byte 8   Byte 9..11
```

### Optimizing by Field Reordering:
By sorting members from largest alignment to smallest:
```c
struct OptimizedLayout {
    int b;       // 4 bytes
    char a;      // 1 byte
    char c;      // 1 byte
                 // [Pad: 2 Bytes to make total size multiple of 4]
};               // Total: 8 Bytes (Saved 33% memory!)
```

---

## 2. The `offsetof` Macro

Provided by `<stddef.h>`, `offsetof(type, member)` returns the exact byte distance from the start of the struct to that member.

---

## 3. Unions & Type Punning

A `union` is a data type where **all members share the exact same memory location**.
The size of a union is equal to the size of its largest member.

```c
union FloatConverter {
    float f;
    uint32_t raw_bits;
};
```
Writing to `.f` and reading from `.raw_bits` allows you to inspect the raw IEEE 754 floating-point binary representation without any costly conversion.

---

## 4. Bitfields

Bitfields allow you to specify exact bit widths for structure members:
```c
struct StatusRegister {
    uint8_t power_on   : 1;  // 1 bit (0 or 1)
    uint8_t error_flag : 1;  // 1 bit
    uint8_t mode       : 2;  // 2 bits (0 to 3)
    uint8_t reserved   : 4;  // 4 bits
};
```

---

## Hands-On Programs

1. [`04_struct_alignment.c`](file:///c:/Users/kkhoie/Downloads/cprog1/04_structs_and_memory_layout/04_struct_alignment.c): Byte-by-byte memory dumping of unaligned, reordered, and `#pragma pack(1)` structs.
2. [`04_unions_and_bitfields.c`](file:///c:/Users/kkhoie/Downloads/cprog1/04_structs_and_memory_layout/04_unions_and_bitfields.c): Floating-point bit inspection via unions and compact hardware status flag manipulation using bitfields.
