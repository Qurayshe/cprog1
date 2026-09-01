# Module 28: Hash Maps & Dynamic Collections (Working Backwards)

In Python (`dict`), Java (`HashMap`), and JavaScript (`Map`), key-value lookups are instantaneous $O(1)$.
How does this work under the hood?

---

## 1. High-Level Concept: The Dictionary / Map

```python
user_roles = {}
user_roles["admin"] = 1
user_roles["guest"] = 2
print(user_roles["admin"]) # 1
```

---

## 2. Low-Level Reality: Hashing Math & Cache-Friendly Open Addressing

A Hash Map is an underlying contiguous C array of **Buckets** (Module 03).

### The 3-Step Low-Level Lookup Pipeline:
1. **Hash the Key to an Integer:**
   Compute a hash integer using an algorithm like DJB2 or FNV-1a:
   `hash = hash_string("admin"); // e.g. 0x9B87F12A`
2. **Modulo Mask to Array Capacity:**
   Convert the 32-bit hash to an array index:
   `index = hash & (capacity - 1); // e.g. 0x9B87F12A & 15 = index 10`
3. **Collision Resolution (Open Addressing vs Chaining):**
   - **Chaining:** Each bucket is a linked list (pointer-heavy, terrible CPU cache performance).
   - **Open Addressing / Linear Probing:** If slot 10 is occupied, check slot 11, 12, 13 in a continuous line. This utilizes **64-byte CPU cache lines** (Module 10) for maximum speed!

```
Array in RAM (Capacity = 8, 64-byte blocks):
Slot 0      Slot 1      Slot 2      ...   Slot 5 ("admin" -> Hash 5)
[ Empty ]   [ "id":9 ]  [ Empty ]   ...   [ "admin":1 ]
```

---

## Hands-On Program

Open and compile [`28_open_addressing_hashmap.c`](file:///c:/Users/kkhoie/Downloads/cprog1/28_hashmaps_and_dynamic_collections/28_open_addressing_hashmap.c) to see a cache-friendly Open Addressing Hash Map with linear probing and automatic dynamic `realloc` expansion in pure C.
