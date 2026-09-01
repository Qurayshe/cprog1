# Module 30: Web Frameworks & HTTP Parsers (Working Backwards)

When you write a web backend in Express.js (`app.get('/api/users')`), Django, or FastAPI:
How does a raw electrical packet on a network card turn into a parsed JSON request in your handler?

---

## 1. High-Level Concept: Web Framework Routes

```python
@app.get("/api/users")
def get_user(request):
    return {"user": "Alice", "role": "admin"}
```

---

## 2. Low-Level Reality: Sockets & Zero-Copy Protocol Framing

### Step 1: The Socket File Descriptor (Module 11)
A web server calls `socket()`, `bind(port 8080)`, and `listen()`.
Incoming TCP connections return a **Socket File Descriptor (`fd`)**.

### Step 2: The Raw Byte Stream
TCP does not have "messages"—it is a continuous, unstructured stream of raw bytes:
```http
GET /api/users HTTP/1.1\r\nHost: example.com\r\nAccept: application/json\r\n\r\n
```

### Step 3: Zero-Copy Token Parsing (Module 03, 21)
High-performance C web servers (like Nginx, Envoy, or Node's `llhttp` C parser) do **not allocate strings for headers**.
They use pointer arithmetic (Module 03) to point directly into the incoming socket buffer:

```
Socket Buffer: [ 'G' | 'E' | 'T' | ' ' | '/' | 'a' | 'p' | 'i' | ... ]
                 ^                       ^
                 |                       |
            method_ptr                path_ptr
```

---

## Hands-On Program

Open and compile [`30_raw_http_parser.c`](file:///c:/Users/kkhoie/Downloads/cprog1/30_http_and_socket_networking/30_raw_http_parser.c) to see a zero-copy HTTP request parser implemented in pure C.
