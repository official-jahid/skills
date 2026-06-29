---
name: c-pointers
description: C pointer patterns - pointer arithmetic, function pointers, void pointers, and common pointer pitfalls for safe C programming.
license: MIT
metadata:
  author: c-skills
  version: "1.0.0"
---

# C Pointers

Essential pointer patterns for safe C programming.

## Pointer Basics

### Declaration and Initialization
```c
int value = 42;
int *ptr = &value;  // Pointer to int
```

### Dereferencing
```c
int value = 42;
int *ptr = &value;
printf("%d\n", *ptr);  // 42
*ptr = 100;            // value becomes 100
```

## Pointer Arithmetic

```c
int arr[] = {1, 2, 3, 4, 5};
int *ptr = arr;  // Points to arr[0]

ptr++;           // Points to arr[1]
*ptr;            // 2
ptr += 2;        // Points to arr[3]
ptr--;           // Points to arr[2]
```

## Function Pointers

```c
int add(int a, int b) { return a + b; }
int (*operation)(int, int) = add;
int result = operation(5, 3);  // 8

// Array of function pointers
int (*operations[])(int, int) = {add, subtract, multiply};
```

## Void Pointers

```c
void *ptr = malloc(sizeof(int));
*(int *)ptr = 42;  // Cast required for dereferencing

// Generic swap function
void swap(void *a, void *b, size_t size) {
    char temp[size];
    memcpy(temp, a, size);
    memcpy(a, b, size);
    memcpy(b, temp, size);
}
```

## Common Pitfalls

- Null pointer dereference: Always check `if (ptr)`
- Dangling pointer: Set to NULL after free
- Buffer overflow: Check array bounds
- Memory leak: Every malloc needs free
- Double free: Track ownership carefully