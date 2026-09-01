# Module 07: Function Pointers & Polymorphism (vtable)

In compiled machine code, functions are just sequences of CPU instructions loaded into the **Text / Code segment** of memory. Because they live at a concrete memory address, you can store their address in a pointer!

---

## 1. Function Pointer Syntax

The syntax for a function pointer can look intimidating at first:
```c
// ReturnType (*PointerName)(ParameterTypes);
int (*operation)(int, int);
```

### Breaking It Down:
```c
int add(int a, int b) { return a + b; }

int (*func_ptr)(int, int) = add;  // 'func_ptr' now holds the entry point address of add()
int result = func_ptr(10, 20);    // Calls add(10, 20) -> 30
```

> [!TIP]
> Use `typedef` to make function pointer signatures clean and readable:
> ```c
> typedef int (*BinaryOp)(int, int);
> BinaryOp op = add;
> ```

---

## 2. Dynamic Dispatch / Jump Tables

Instead of long `switch-case` or `if-else` chains, jump tables provide $O(1)$ dynamic instruction dispatch:
```c
BinaryOp operations[] = {add, subtract, multiply, divide};
int result = operations[opcode](x, y); // Instant O(1) jump
```

---

## 3. How C++ and OOP Work Under the Hood: The Virtual Table (`vtable`)

Object-Oriented languages like C++ implement runtime polymorphism (`virtual` functions) using **Virtual Method Tables (vtables)**. You can do the exact same thing cleanly in pure C:

```
Object (Shape)                 VTable (ShapeOps)
+--------------------+        +------------------------------+
| vtable_ptr --------|------> | draw()     -> draw_circle()  |
| int color          |        | get_area() -> area_circle()  |
| float radius       |        +------------------------------+
+--------------------+
```

Every "instance" contains a pointer to a struct of function pointers. Calling `shape->vtable->draw(shape)` dynamically executes the correct implementation for circles, rectangles, or triangles!

---

## Hands-On Program

Open and compile [`07_function_pointers_and_vtables.c`](file:///c:/Users/kkhoie/Downloads/cprog1/07_function_pointers_and_vtables/07_function_pointers_and_vtables.c) to see generic callbacks, a jump table calculator, and a complete polymorphic Shape/Device driver vtable implementation.
