# Module 27: Async/Await & Event Loops (Working Backwards)

In Node.js, Python (`asyncio`), and Rust (`tokio`), single-threaded servers can handle 100,000 simultaneous network connections without creating 100,000 OS threads. How?

---

## 1. High-Level Concept: Async / Await & Promises

```javascript
async function handleUser() {
    let data = await databaseQuery(); // Yields execution
    console.log(data);                // Resumes when data arrives
}
```

---

## 2. Low-Level Reality: Non-Blocking I/O & The Event Loop

### The Kernel Mechanism: `epoll` / `kqueue` / `select` (Module 11)
Instead of blocking a thread on `read()`, the thread puts all file descriptors in non-blocking mode (`O_NONBLOCK`).
The thread asks the OS kernel: *"Here are 50,000 socket FDs. Wake me up when any of them has new data ready."*

### The Event Loop Engine:
A single thread runs a tight loop processing a FIFO **Callback Task Queue**:

```
+-------------------------------------------------------------+
| 1. POLL OS KERNEL (epoll/select) for completed I/O events   |
+-------------------------------------------------------------+
                              |
                              v
| 2. PUSH corresponding callbacks into Task Queue             |
+-------------------------------------------------------------+
                              |
                              v
| 3. EXECUTE callbacks one-by-one until queue is empty        |
+-------------------------------------------------------------+
                              |
                              +---> (Loop repeats!)
```

### How `async/await` is Compiled: State Machine Transformation
When a function contains `await`, the compiler secretly rewrites it into a **State Machine struct** with an integer `state` field:
```c
struct Coroutine {
    int state; // 0: start, 1: waiting for DB, 2: completed
    void* local_variables;
};
```
When you `await`, the function saves its local variables to the struct, sets `state = 1`, and **returns immediately** so the event loop can process other tasks!

---

## Hands-On Program

Open and compile [`27_micro_event_loop.c`](file:///c:/Users/kkhoie/Downloads/cprog1/27_async_event_loop_and_coroutines/27_micro_event_loop.c) to see a complete non-blocking event loop and stackless coroutine state machine in pure C.
