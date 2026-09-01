# Module 24: Capstone: Lock-Free SPSC Ring Buffer Queue

In high-frequency trading (HFT), audio DSP, and game engine audio/render pipelines, traditional mutex locks cause unpredictable OS thread scheduling pauses.

A **Lock-Free Single-Producer Single-Consumer (SPSC) Queue** allows one producer thread to push data and one consumer thread to pop data with **sub-microsecond latency and ZERO mutex locks**.

---

## 1. The Ring Buffer Architecture

A fixed-size array wraps around using modulo power-of-two arithmetic (`index & (Capacity - 1)`).

```
Ring Buffer Slots (Capacity = 4)
+------------+------------+------------+------------+
| Slot 0     | Slot 1     | Slot 2     | Slot 3     |
+------------+------------+------------+------------+
      ^                         ^
      |                         |
  head (Consumer reads)     tail (Producer writes)
```

- **Producer updates `tail`** using `std::memory_order_release`.
- **Consumer updates `head`** using `std::memory_order_release`.
- `alignas(64)` is used on `head` and `tail` to prevent **False Sharing** (Module 23).

---

## 2. Lock-Free Atomic Memory Ordering

- **`memory_order_release`:** Ensures all previous writes (the payload data) are globally visible before the index variable is updated.
- **`memory_order_acquire`:** Ensures subsequent reads cannot begin until the index update is seen.

---

## 🔬 Hands-On Program

Open and compile [`24_spsc_ring_buffer.cpp`](file:///c:/Users/kkhoie/Downloads/cprog1/24_lock_free_spsc_ring_buffer/24_spsc_ring_buffer.cpp) to see a complete, high-performance Lock-Free SPSC Queue streaming 1,000,000 messages across threads in pure C++.
