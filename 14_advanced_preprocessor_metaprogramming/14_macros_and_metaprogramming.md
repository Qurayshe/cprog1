# Module 14: Preprocessor Metaprogramming & X-Macros

The C preprocessor is a Turing-complete macro language executed before the compiler ever sees your code. Mastering advanced macro patterns allows you to write self-generating, DRY (Don't Repeat Yourself) systems.

---

## 1. Essential Advanced Macro Operators

1. **Stringification (`#`)**: Converts a macro parameter directly into a quoted C string literal:
   ```c
   #define PRINT_EXPR(expr) printf(#expr " = %d\n", expr)
   PRINT_EXPR(5 + 10); // Expands to: printf("5 + 10" " = %d\n", 5 + 10);
   ```

2. **Token Concatenation (`##`)**: Glues two tokens together into a single new identifier at compile time:
   ```c
   #define DECLARE_GETTER(type, name) \
       type get_##name(void) { return g_##name; }
   ```

3. **Variadic Macros (`__VA_ARGS__`)**: Accepts variable numbers of arguments:
   ```c
   #define LOG_DEBUG(fmt, ...) \
       printf("[DEBUG %s:%d] " fmt "\n", __FILE__, __LINE__, __VA_ARGS__)
   ```

---

## 2. The Power of X-Macros

One of the most famous patterns in production C codebases (such as the Linux Kernel, Redis, and SQLite) is the **X-Macro Pattern**.

### The Problem:
You have an `enum` of error codes or commands. Whenever you add a new code, you must manually update:
1. The `enum` definition.
2. A `const char* to_string(code)` function.
3. A command handler jump table.
*If you forget one, you get desynchronized bugs.*

### The X-Macro Solution:
Define the master list **once** as an expandable table:

```c
#define COLOR_TABLE(X) \
    X(COLOR_RED,   0xFF0000, "Red")   \
    X(COLOR_GREEN, 0x00FF00, "Green") \
    X(COLOR_BLUE,  0x0000FF, "Blue")

// 1. Generate the enum automatically:
#define AS_ENUM(name, hex, str) name,
enum Color { COLOR_TABLE(AS_ENUM) };

// 2. Generate the string converter automatically:
#define AS_STRING(name, hex, str) case name: return str;
const char* color_name(enum Color c) {
    switch (c) { COLOR_TABLE(AS_STRING) }
    return "Unknown";
}
```
Now adding a color only requires adding **one single line** to `COLOR_TABLE`!

---

## 🔬 Hands-On Program

Open and compile [`14_xmacros_and_codegen.c`](file:///c:/Users/kkhoie/Downloads/cprog1/14_advanced_preprocessor_metaprogramming/14_xmacros_and_codegen.c) to see X-Macros, token pasting code generators, and stringification in action.
