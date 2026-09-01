# Module 01: The Compilation Pipeline & Data Types

Understanding C requires understanding what happens between the text you write in a `.c` file and the physical electrical signals executed by the CPU.

---

## 1. The 4 Stages of Compilation

When you compile a C program, it passes through four distinct tools that transform human-readable text into CPU machine instructions.

```
                  +-------------------------+
                  | Source Code: main.c     |
                  +-------------------------+
                               |
                               v
                     [ 1. PREPROCESSOR ]
                     (cpp / expands #include, #define,
                      strips comments)
                               |
                               v
                  +-------------------------+
                  | Preprocessed: main.i    |
                  +-------------------------+
                               |
                               v
                      [ 2. COMPILER ]
                      (cc1 / converts C code into
                       target CPU assembly mnemonics)
                               |
                               v
                  +-------------------------+
                  | Assembly: main.s        |
                  +-------------------------+
                               |
                               v
                      [ 3. ASSEMBLER ]
                      (as / translates assembly
                       into binary machine opcodes)
                               |
                               v
                  +-------------------------+
                  | Object File: main.o     |
                  +-------------------------+
                               |
                               v
                        [ 4. LINKER ]
                        (ld / resolves external symbols,
                         links libc & produces executable)
                               |
                               v
                  +-------------------------+
                  | Executable: main (.exe) |
                  +-------------------------+
```

---

### What the Extensions Mean:

| Extension | What it Stands For | File Format | Purpose |
| :--- | :--- | :--- | :--- |
| **`.c`** | **C Source File** | Plain Text | The original human-written C code. |
| **`.i`** | **Intermediate (or Included)** | Plain Text | Pure C code after macro expansion and header inclusion (`#include`). Comments are stripped. |
| **`.s`** / **`.asm`** | **Symbolic Assembly** | Plain Text | Architecture-specific assembly language instructions (e.g., x86-64, ARM, RISC-V). |
| **`.o`** / **`.obj`** | **Object File** | Binary | Relocatable machine code (binary CPU opcodes) with an unresolved symbol table. |
| **`.exe`** / (none on Linux) | **Executable Binary** | Binary (PE on Windows, ELF on Linux) | Final runnable binary with all symbols resolved and ready for the OS loader. |

---

### 🔍 Concrete Example: What Each File Looks Like

Suppose we have a minimal `main.c`:

```c
// main.c
#define MULTIPLIER 3

int compute(int x) {
    return x * MULTIPLIER;
}

int main(void) {
    int result = compute(10);
    return result;
}
```

---

#### 1. Preprocessed File (`main.i`)
Generated with: `gcc -E main.c -o main.i`
- All `#define` macros are substituted (e.g., `MULTIPLIER` replaced with `3`).
- All comments are removed.
- If you had `#include <stdio.h>`, thousands of lines of standard function declarations from the header would be pasted directly into this file.

```c
# 1 "main.c"
# 1 "<built-in>"
# 1 "<command-line>"
# 1 "main.c"

int compute(int x) {
    return x * 3;
}

int main(void) {
    int result = compute(10);
    return result;
}
```

---

#### 2. Assembly File (`main.s`)
Generated with: `gcc -S main.c -o main.s` (or with optimization: `gcc -S -O2 main.c -o main.s`)
- This is human-readable CPU assembly language (e.g., x86-64 instructions like `mov`, `imul`, `call`, `ret`):

```assembly
	.file	"main.c"
	.text
	.globl	compute
	.type	compute, @function
compute:
	pushq	%rbp
	movq	%rsp, %rbp
	movl	%edi, -4(%rbp)
	movl	-4(%rbp), %eax
	imull	$3, %eax, %eax        # Multiplies input by 3
	popq	%rbp
	ret

	.globl	main
	.type	main, @function
main:
	pushq	%rbp
	movq	%rsp, %rbp
	subq	$16, %rsp
	movl	$10, %edi
	call	compute               # Calls compute(10)
	movl	%eax, -4(%rbp)
	movl	-4(%rbp), %eax
	leave
	ret
```

---

#### 3. Object File (`main.o` or `main.obj`)
Generated with: `gcc -c main.s -o main.o` (or directly from C: `gcc -c main.c -o main.o`)
- This is **binary machine code** (bytes like `0x55 0x48 0x89 0xE5 ...`).
- Viewing it directly in a text editor looks like garbage characters, but you can inspect it using a disassembler or hex viewer (e.g., `objdump -d main.o` or `xxd main.o`):

```
00000000 <compute>:
   0:   55                      push   %rbp
   1:   48 89 e5                mov    %rsp,%rbp
   4:   89 7d fc                mov    %edi,-0x4(%rbp)
   7:   8b 45 fc                mov    -0x4(%rbp),%eax
   a:   6b c0 03                imul   $0x3,%eax,%eax
   d:   5d                      pop    %rbp
   e:   c3                      ret
```

---

#### 4. Final Executable (`main.exe` / `main`)
Generated with: `gcc main.o -o main` (or `gcc main.c -o main`)
- The linker (`ld`) takes all object files, resolves cross-file function calls (e.g. `printf` into `libc`), adds startup code (`_start`), and creates the final binary.

---

### 🛠️ Commands to Create All Intermediate Files Step-by-Step

You can run each stage independently or tell GCC to keep all intermediate files automatically:

```bash
# Method A: Step-by-step manual execution
gcc -E main.c -o main.i      # 1. Preprocess only -> creates main.i
gcc -S main.i -o main.s      # 2. Compile to assembly -> creates main.s
gcc -c main.s -o main.o      # 3. Assemble to object -> creates main.o
gcc main.o -o main.exe       # 4. Link into final executable

# Method B: One command to generate the executable AND save all intermediate files:
gcc -save-temps main.c -o main.exe
```

---

## 2. Fundamental Data Types & Memory Representation

In high-level languages like Python or JavaScript, numbers are abstract values. In C, **every type maps to a concrete byte size and hardware interpretation**.

### Standard Types (Typical 64-bit Architecture):
| Type | Width (typical) | Range (Signed) | Format Specifier |
| :--- | :--- | :--- | :--- |
| `char` | 1 byte (8 bits) | -128 to 127 (or 0 to 255 if unsigned) | `%c` or `%d` |
| `short` | 2 bytes (16 bits) | -32,768 to 32,767 | `%hd` |
| `int` | 4 bytes (32 bits) | -2,147,483,648 to 2,147,483,647 | `%d` or `%i` |
| `long` | 4 or 8 bytes* | OS/arch dependent (4 on Win64, 8 on Linux64) | `%ld` |
| `long long` | 8 bytes (64 bits) | $\approx -9.22 \times 10^{18}$ to $9.22 \times 10^{18}$ | `%lld` |
| `float` | 4 bytes (IEEE 754) | 6-7 decimal digits precision | `%f` |
| `double` | 8 bytes (IEEE 754) | 15-17 decimal digits precision | `%lf` |
| `size_t` | Machine word (8 bytes on 64-bit) | Unsigned size of objects in memory | `%zu` |
| `uintptr_t` | Machine word (8 bytes on 64-bit) | Integer capable of holding a pointer address | `%zu` / `%p` |

> [!TIP]
> **Best Practice in Modern Low-Level C:** Always include `<stdint.h>` when exact byte width matters (e.g., file formats, networking protocols, hardware drivers):
> - `uint8_t`, `uint16_t`, `uint32_t`, `uint64_t` (Unsigned)
> - `int8_t`, `int16_t`, `int32_t`, `int64_t` (Signed)

---

## 3. How Integers Are Stored: Two's Complement

Computers use **Two's Complement** representation for signed integers.

### How to calculate negative numbers in 8-bit Two's Complement:
For $-5$:
1. Take binary of $+5$: `0000 0101`
2. Invert all bits (One's complement): `1111 1010`
3. Add 1: `1111 1011`

### Why Two's Complement is brilliant:
Addition and subtraction use the exact same CPU silicon circuitry regardless of whether the numbers are signed or unsigned:
```
   5:  0000 0101
+ -5:  1111 1011
----------------
   0: (1) 0000 0000  (The 9th carry bit is discarded by the 8-bit register)
```

---

## 4. Integer Overflow vs Wraparound

- **Unsigned Overflow:** Defined by the C standard to **wrap around** modulo $2^N$.
  `uint8_t x = 255; x = x + 1; // x becomes 0`
- **Signed Overflow:** In C, signed overflow is **Undefined Behavior (UB)**! The compiler is legally permitted to optimize assuming signed overflow never occurs, which can lead to unexpected vulnerabilities.

---

## 🔬 Hands-On Program

Open and compile [`01_types_and_bits.c`](file:///c:/Users/kkhoie/Downloads/cprog1/01_compilation_and_memory/01_types_and_bits.c) to inspect the byte sizes and memory bit patterns directly on your machine.
