# Module 13: Virtual Memory, Pages & Zero-Copy I/O

Pointers in your program do not refer to physical RAM pins. They refer to **Virtual Memory Addresses**, which the CPU's **Memory Management Unit (MMU)** translates at hardware speed.

---

## 1. The Multi-Level Page Table & MMU Translation

The CPU's **Memory Management Unit (MMU)** sits between the processor cores and physical RAM.

Memory is divided into **4,096-byte (4 KB) blocks called Pages** (and in RAM, they are called **Page Frames**).

```
   64-bit Virtual Address (e.g. 0x00007FFF12345678)
   +---------------------------------------+--------------------+
   |    Virtual Page Number (VPN)          | Page Offset (12b)  |
   |           (Bits 12 - 47)              |   (Bits 0 - 11)    |
   +---------------------------------------+--------------------+
                      |                               |
                      v                               | (Untouched)
             [ MMU Page Table ]                       |
             VPN 0x7FFF12345 ---> Frame 0x00000008    |
                                      |               |
                                      v               v
                   Physical RAM: [ Frame 0x00000008 | Offset 0x678 ]
```

### Why 12 Bits for the Offset?
$$2^{12} = 4096\text{ bytes (4 KB)}$$
The lowest 12 bits of any address represent the exact byte position inside a 4 KB page:
- `page_offset = address & 0x0FFF;` (Mask the lowest 12 bits)
- `page_base   = address & ~0x0FFF;` (Zero out the lowest 12 bits)

---

## 2. Page Faults: How `malloc` Actually Works (Demand Paging)

When you ask for memory with `malloc(1024 * 1024 * 100)` (100 MB):
1. The OS **does NOT immediately allocate 100 MB of physical RAM**.
2. It simply allocates virtual address space and sets the Page Table entries to **"Not Present" (`is_present = 0`)**.
3. When your program actually reads or writes to `ptr[0]`, the CPU MMU detects `is_present == 0` and triggers a hardware **Page Fault Exception**.
4. The OS kernel catches the Page Fault, grabs 1 physical 4 KB frame of RAM from its free list, zeroes it out, maps it into your page table (`is_present = 1`), and resumes your code.
5. This is called **Lazy Allocation (Demand Paging)**: physical RAM is only consumed when touched.

---

## 3. Memory Protection & The NX / DEP Bit

Every entry in the page table has hardware permission flags:
- **`R` (Read):** Can data be read?
- **`W` (Write):** Can data be modified?
- **`X` / `NX` (Execute / No-Execute):** Can the CPU run instructions from this address?

```
Segment         Permissions     Purpose
--------------------------------------------------------------------------------
.text (Code)    Read + Execute  Stores compiled machine instructions.
.rodata         Read Only       Stores string literals ("Hello"). Writing crashes!
.data / Heap    Read + Write    Stores dynamic variables. Execution is BLOCKED (NX bit).
Stack           Read + Write    Stores locals. Execution is BLOCKED to prevent buffer overflows.
```

> [!IMPORTANT]
> The **NX (No-Execute / DEP)** bit is a silicon-level security feature. If a buffer overflow injects executable machine code onto the stack or heap, the CPU refuses to execute it and immediately terminates the process with an Access Violation.

---

## 4. Memory-Mapped Files (`mmap`) & Zero-Copy I/O

Traditional file reading requires **two copies**:
```
[ Disk ] --(1. DMA)--> [ OS Kernel Page Cache ] --(2. memcpy)--> [ User Buffer (malloc) ]
```

With `mmap()`:
The OS kernel configures your process's **Page Table to point directly to the OS disk cache frames in RAM**:

```
[ Disk ] --(DMA)--> [ OS Page Cache / Physical RAM ]
                              ^
                              | (Direct Page Table Mapping)
                 [ User Address Space (char *data) ]
```

### Why High-Performance Systems (Databases, Game Engines) Use `mmap`:
1. **Zero Copies:** Reading `data[500]` reads directly from physical cache memory without copying.
2. **Instant Open:** A 100 GB file can be mapped in **under 1 millisecond** because no data is loaded until individual 4 KB pages are accessed.
3. **Automatic Persistence:** Writing `data[i] = 'A'` marks the page table entry as **Dirty (`is_dirty = 1`)**. The OS kernel automatically flushes dirty pages back to the physical SSD in the background!

---

## 🔬 Hands-On Program

Open and compile [`13_virtual_memory_pages.c`](file:///c:/Users/kkhoie/Downloads/cprog1/13_virtual_memory_and_mmap/13_virtual_memory_pages.c) to inspect page boundaries, page offsets, and virtual memory alignment math.
