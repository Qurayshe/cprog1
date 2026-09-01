# Module 16: Building a Bytecode Virtual Machine (Interpreter)

How do languages like Python, Java (JVM), Lua, and WebAssembly execute code?
They compile source text into an array of compact binary instructions called **Bytecode**, and run a virtual processor loop written in C.

---

## 1. Stack Machine Architecture

In a **Stack-Based Virtual Machine**, operands are pushed onto and popped off an execution stack:

```
Example: Calculate (5 + 10) * 2

Bytecode stream:
1. OP_PUSH 5      -> Stack: [ 5 ]
2. OP_PUSH 10     -> Stack: [ 5, 10 ]
3. OP_ADD         -> Pops 10 & 5, adds them, pushes 15 -> Stack: [ 15 ]
4. OP_PUSH 2      -> Stack: [ 15, 2 ]
5. OP_MUL         -> Pops 2 & 15, multiplies, pushes 30 -> Stack: [ 30 ]
6. OP_PRINT       -> Prints 30
7. OP_HALT        -> Stops execution
```

---

## 2. The Fetch-Decode-Execute Loop

The core engine of every interpreter is an elegant loop:

```c
while (running) {
    // 1. FETCH instruction byte
    uint8_t opcode = bytecode[ip++];

    // 2. DECODE & EXECUTE
    switch (opcode) {
        case OP_PUSH:  push(bytecode[ip++]); break;
        case OP_ADD:   push(pop() + pop()); break;
        case OP_PRINT: printf("%d\n", pop()); break;
        case OP_HALT:  running = false; break;
    }
}
```

---

## 🔬 Hands-On Program

Open and compile [`16_stack_vm.c`](file:///c:/Users/kkhoie/Downloads/cprog1/16_bytecode_vm_interpreter/16_stack_vm.c) to inspect and run a complete, fully functional Bytecode Virtual Machine written in ~90 lines of clean C.
