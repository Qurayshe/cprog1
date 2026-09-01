# Module 08: Binary I/O & Custom File Formats

While text formats like JSON or CSV are human-readable, binary formats are significantly faster, use far less disk space, and avoid CPU-intensive parsing and string conversions.

---

## 1. Text vs Binary Files

| Feature | Text Files (`"r"`, `"w"`) | Binary Files (`"rb"`, `"wb"`) |
| :--- | :--- | :--- |
| **Storage** | ASCII/UTF-8 character codes | Exact memory byte dump |
| **Number 12345678** | 8 bytes (`'1' '2' '3' '4' '5' '6' '7' '8'`) | 4 bytes (`0x00BC614E`) |
| **Line Endings** | Auto-translated (`\n` <-> `\r\n` on Windows) | Untouched 1:1 raw bytes |
| **Speed** | Slow (requires `atoi`/`sscanf` parsing) | Fast (single `fread` into RAM) |

---

## 2. Binary File I/O Functions

From `<stdio.h>`:
- `size_t fwrite(const void *ptr, size_t size, size_t count, FILE *stream)`
- `size_t fread(void *ptr, size_t size, size_t count, FILE *stream)`
- `int fseek(FILE *stream, long offset, int whence)` (`SEEK_SET`, `SEEK_CUR`, `SEEK_END`)
- `long ftell(FILE *stream)` (returns current byte position in file)

---

## 3. Designing a Robust Binary File Format

Real-world binary formats (PNG, ELF, ZIP, MP4) always structure their data in 3 parts:

```
+-------------------------------------------------------------+
| FILE HEADER (Fixed Size)                                    |
|   - Magic Bytes: 4 bytes identifier (e.g. "PK\x03\x04")      |
|   - Version: uint32_t                                       |
|   - Record Count: uint32_t                                  |
+-------------------------------------------------------------+
| RECORD TABLE / PAYLOAD                                      |
|   - Record 1 [ ID (4B) | Name (32B) | Score (4B) ]          |
|   - Record 2 [ ID (4B) | Name (32B) | Score (4B) ]          |
|   - ...                                                     |
+-------------------------------------------------------------+
| CHECKSUM / FOOTER (Optional integrity verification)        |
+-------------------------------------------------------------+
```

### Why Magic Numbers Matter:
A **Magic Number** (e.g., `0x544F4F4C` or `'T''O''O''L'`) at offset 0 guarantees that your program doesn't accidentally parse an MP3 file as an image and crash!

---

## Hands-On Program

Open and compile [`08_binary_file_records.c`](file:///c:/Users/kkhoie/Downloads/cprog1/08_binary_io_and_serialization/08_binary_file_records.c) to see complete creation, writing, seeking, reading, and header validation of a custom binary file format.
