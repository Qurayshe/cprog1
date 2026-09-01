# Module 06: Bitwise Operations, Endianness & Hardware Registers

At the lowest level, all hardware communication (network cards, GPIO pins, graphics chips, flash memory) is controlled by manipulating individual bits in registers.

---

## 1. The 6 Bitwise Operators

| Operator | Name | Logic | Example (`0b1010` & `0b1100`) |
| :--- | :--- | :--- | :--- |
| `&` | Bitwise AND | 1 if **both** are 1 | `0b1000` |
| `\|` | Bitwise OR | 1 if **either** is 1 | `0b1110` |
| `^` | Bitwise XOR | 1 if **different** | `0b0110` |
| `~` | Bitwise NOT | Inverts all bits | `~0b1010` = `...0101` |
| `<<` | Shift Left | Multiplies by $2^N$ | `1 << 3` = 8 (`0b1000`) |
| `>>` | Shift Right | Divides by $2^N$ | `8 >> 2` = 2 (`0b0010`) |

---

## 2. Essential Bit Manipulation Recipes

Here are the standard idioms used in every operating system kernel and embedded firmware:

```c
// 1. SET the Nth bit to 1:
value |= (1U << n);

// 2. CLEAR the Nth bit to 0:
value &= ~(1U << n);

// 3. TOGGLE / FLIP the Nth bit:
value ^= (1U << n);

// 4. CHECK if the Nth bit is set:
bool is_set = (value & (1U << n)) != 0;

// 5. EXTRACT a range of bits (e.g., bits 4-7):
uint8_t field = (value >> 4) & 0x0F;

// 6. FAST POWER-OF-TWO CHECK:
bool is_pow2 = (x > 0) && ((x & (x - 1)) == 0);
```

---

## 3. Endianness: Little-Endian vs Big-Endian

Endianness defines the **order in which individual bytes of a multi-byte word are stored in memory**.

Given the 32-bit integer `0x12345678`:
- **Little-Endian (x86, x64, ARM default):** The Least Significant Byte (`0x78`) is stored at the lowest memory address.
  ```
  Address:   0x00   0x01   0x02   0x03
  Value:   [ 0x78 | 0x56 | 0x34 | 0x12 ]
  ```
- **Big-Endian (Network Byte Order, older Motorola/PowerPC):** The Most Significant Byte (`0x12`) is stored at the lowest memory address.
  ```
  Address:   0x00   0x01   0x02   0x03
  Value:   [ 0x12 | 0x34 | 0x56 | 0x78 ]
  ```

---

## 4. Emulating Memory-Mapped I/O (MMIO)

In embedded systems and device drivers, hardware registers are mapped to physical memory addresses:
```c
// volatile tells the compiler: "Never optimize away reads/writes to this address!"
#define UART_STATUS_REG (*(volatile uint32_t *)0x40001000)
```

---

## 🔬 Hands-On Programs

1. [`06_bitwise_manipulation.c`](file:///c:/Users/kkhoie/Downloads/cprog1/06_bitwise_and_registers/06_bitwise_manipulation.c): Setting, clearing, toggling, bit extraction, and binary bit printing.
2. [`06_endianness_and_registers.c`](file:///c:/Users/kkhoie/Downloads/cprog1/06_bitwise_and_registers/06_endianness_and_registers.c): Hardware endianness detection, byte-swapping macros, and simulated hardware UART peripheral control.
