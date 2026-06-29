---
name: c-memory-management
description: C memory management patterns - malloc, calloc, realloc, free, memory leaks prevention, and pointer safety for systems programming.
license: MIT
metadata:
  author: c-skills
  version: "1.0.0"
---

# C Memory Management

Essential patterns for safe memory allocation and deallocation in C.

## Dynamic Memory Allocation

### malloc - Allocate memory
```c
#include <stdlib.h>

int *arr = malloc(10 * sizeof(int));
if (arr == NULL) {
    // Handle allocation failure
    exit(EXIT_FAILURE);
}
// Use the memory
free(arr);
arr = NULL; // Prevent dangling pointer
```

### calloc - Zero-initialized allocation
```c
int *arr = calloc(10, sizeof(int)); // Initializes to zero
```

### realloc - Resize allocated memory
```c
int *arr = realloc(arr, 20 * sizeof(int));
if (arr == NULL) {
    // Handle failure
}
```

## Best Practices

- Always check return value for NULL
- Always free allocated memory
- Set pointer to NULL after freeing
- Use `sizeof(type)` for portability
- Prefer calloc for arrays, malloc for single objects

## Common Patterns

### String Duplication
```c
char *strdup(const char *s) {
    size_t len = strlen(s) + 1;
    char *p = malloc(len);
    if (p != NULL) {
        memcpy(p, s, len);
    }
    return p;
}
```

### Safe Free Wrapper
```c
#define SAFE_FREE(ptr) do { \
    if (ptr) { \
        free(ptr); \
        ptr = NULL; \
    } \
} while(0)
```

## Valgrind for Leak Detection
```bash
valgrind --leak-check=full ./program
```