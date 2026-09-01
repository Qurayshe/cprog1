# Module 11: OS System Calls & Signals

Your user program does not have direct permission to touch hard drives, network cards, or screen hardware. To interact with the physical world, your program must request services from the **Operating System Kernel** via **System Calls** (syscalls).

---

## 1. User Space vs Kernel Space: Privilege Rings

Modern x86-64 and ARM processors enforce hardware security levels called **Privilege Rings**:

```
+-------------------------------------------------------------+
| Ring 3: User Space (Your C Program)                         |
|   - Can only access its own memory pages                    |
|   - Cannot execute privileged CPU instructions (e.g., CLI,  |
|     LIDT, MOV CR3, IN/OUT port I/O)                         |
|   - Direct access to hardware/disk is BLOCKED by silicon    |
+-------------------------------------------------------------+
                              |
      === [ SYSCALL / SYSENTER Instruction Boundary ] ===
                              |
+-------------------------------------------------------------+
| Ring 0: Kernel Space (Windows NT Kernel / Linux Kernel)     |
|   - Unrestricted access to all physical RAM, CPU registers, |
|     PCIe devices, hard drives, and network cards            |
+-------------------------------------------------------------+
```

### What Happens Mechanically During `write(1, "Hi\n", 3)`:
1. **Prepare Registers:** The C library loads the system call number (e.g. `1` for `sys_write` on Linux x86-64) into register `RAX`, file descriptor `1` into `RDI`, buffer pointer into `RSI`, and count (`3`) into `RDX`.
2. **The Hardware Trap:** The CPU executes the `syscall` instruction.
3. **Privilege Transition:** The CPU hardware instantly switches from **Ring 3 to Ring 0**, saves the user instruction pointer (`RIP`) into `RCX`, switches to the **Kernel Stack**, and jumps to the kernel's central syscall dispatcher.
4. **Validation:** The kernel verifies that the buffer memory address in `RSI` belongs to the user process (preventing user programs from tricking the kernel into reading private kernel memory).
5. **Execution:** The kernel writes the bytes to the display/TTY driver.
6. **Return (`sysret`):** The CPU drops back to Ring 3 and resumes your C program immediately after the `syscall` instruction.

```
+-----------------------------------------------------------+
| [ USER APPLICATION ]                                      |
|    printf("Hello");                                       |
+-----------------------------------------------------------+
              | (Standard C Library wrapper)
              v
| [ LIBC ]    write(1, "Hello", 5);                         |
+-----------------------------------------------------------+
              | (Software Interrupt / 'syscall' CPU instruction)
              v
====== USER / KERNEL BOUNDARY (Privilege Switch) ============
              |
              v
| [ OS KERNEL (Ring 0) ]                                    |
|    - Checks buffer memory validity                        |
|    - Dispatches to File System / TTY Driver               |
|    - Outputs characters to display hardware               |
+-----------------------------------------------------------+
```

---

## 2. File Descriptors Under the Hood

When you open a file or socket, the OS doesn't return raw pointers to disk sectors. It returns an integer called a **File Descriptor (FD)**:
- `0`: Standard Input (`stdin`)
- `1`: Standard Output (`stdout`)
- `2`: Standard Error (`stderr`)

```
Process Memory Space         OS Kernel Data Structures
[ Your C App ]
  fd = 3 -------------> [ Process FD Table ]
                            Slot 3: Pointer to File Object
                                      |
                                      v
                            [ System-Wide Open File Table ]
                              - Current Seek Offset (e.g., byte 1024)
                              - Flags (O_RDONLY / O_SYNC)
                                      |
                                      v
                            [ V-Node / Inode Cache ]
                              - Actual physical sectors on NVMe/SSD
```

Because the OS kernel maintains this lookup table, it can enforce file permissions, handle buffering, and ensure process isolation.

---

## 3. Asynchronous Signal Handling & Reentrancy

A **Signal** is a software interrupt delivered by the OS kernel to notify your program of an external event (e.g. `SIGINT` when the user presses `Ctrl+C`, `SIGSEGV` on illegal memory access, or `SIGTERM`).

### How Signal Delivery Works:
1. The kernel pauses your user program at whatever assembly instruction it is currently executing.
2. The kernel injects a new temporary frame into the stack, pointing the instruction pointer (`RIP`) to your registered signal handler function.
3. Your handler runs.
4. When your handler returns, the `sigreturn` system call restores all saved CPU registers and resumes the main thread where it was paused.

```
Main Thread:   [ Instruction A ] -> [ Instruction B ] ------------------------> [ Instruction C ]
                                             |                                        ^
                                   (Interrupt by SIGINT)                              |
                                             v                                        |
Signal Handler:                       [ handle_sigint() ] -> [ return from handler ] -+
```

### ⚠️ The Reentrancy Danger (Why `printf` and `malloc` crash in handlers):
If the main thread is in the middle of executing `malloc()`, it holds an internal heap mutex lock.
If a signal arrives and the signal handler also calls `malloc()` or `printf()`:
1. The handler attempts to acquire the heap lock already held by the same thread.
2. The thread deadlocks or corrupts heap memory!

### The Solution: `volatile sig_atomic_t`
```c
// Guaranteed to be read and written in a single CPU instruction without tearing
static volatile sig_atomic_t g_shutdown_requested = 0;

void handle_sigint(int signum) {
    (void)signum;
    g_shutdown_requested = 1; // Safe: lock-free, single atomic store
}
```

---

## 🔬 Hands-On Program

Open and compile [`11_signals_and_syscalls.c`](file:///c:/Users/kkhoie/Downloads/cprog1/11_syscalls_and_signals/11_signals_and_syscalls.c) to see clean signal handling, graceful shutdown flags, and low-level I/O mechanics.
