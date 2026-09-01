# Module 31: JIT Compilers (V8, JVM) (Working Backwards)

In modern JavaScript (V8 / Node.js) and Java (JVM HotSpot), programs initially run as an interpreted bytecode loop (Module 16).
When a function runs frequently ("hot loop"), the engine **JIT (Just-In-Time) compiles** the bytecode into raw x86-64 machine code in RAM and runs at C speed!

---

## 1. High-Level Concept: Just-In-Time Compilation

```javascript
function add(a, b) {
    return a + b; // Runs 1,000,000 times -> JIT compiles to native CPU machine code!
}
```

---

## 2. Low-Level Reality: Allocating Executable Pages & Emitting Opcodes

How can a C program write bytes to RAM and then tell the CPU to **execute** those bytes as code?

### Step 1: Allocate Executable Virtual Memory Pages (Module 13)
Standard `malloc()` pages have the **NX (No-Execute)** bit set (Module 13). Attempting to jump to a `malloc` buffer crashes with a Segmentation Fault.
The JIT engine uses OS system calls to request **executable pages**:
- On Windows: `VirtualAlloc(..., PAGE_EXECUTE_READWRITE)`
- On Linux/macOS: `mmap(..., PROT_READ | PROT_WRITE | PROT_EXEC, MAP_ANONYMOUS, ...)`

### Step 2: Write CPU Machine Byte Opcodes (Module 01)
To generate an `add(int a, int b)` function on x86-64:
- `8D 04 37` = `lea eax, [rdi + rsi]` (Add argument 1 + argument 2)
- `C3`       = `ret` (Return to caller)

```c
uint8_t machine_code[] = {
    0x8D, 0x04, 0x37,  // lea eax, [rdi + rsi] (add registers)
    0xC3               // ret
};
memcpy(exec_page, machine_code, sizeof(machine_code));
```

### Step 3: Cast Memory Address to a C Function Pointer (Module 07)
```c
typedef int (*JitFunc)(int, int);
JitFunc func = (JitFunc)exec_page;
int result = func(15, 25); // Jumps CPU instruction pointer (RIP) straight into generated memory!
```

---

## Hands-On Program

Open and compile [`31_mini_jit_compiler.c`](file:///c:/Users/kkhoie/Downloads/cprog1/31_jit_compiler_and_dynamic_codegen/31_mini_jit_compiler.c) to see a real working JIT compiler generating machine code in memory and executing it.
