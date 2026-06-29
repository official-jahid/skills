---
name: cpp-memory-management
description: C++ memory management patterns - RAII, smart pointers, move semantics, and modern C++ memory safety practices (C++11/14/17/20).
license: MIT
metadata:
  author: cpp-skills
  version: "1.0.0"
---

# C++ Memory Management

Modern C++ memory management using RAII and smart pointers.

## Smart Pointers

### unique_ptr - Exclusive ownership
```cpp
#include <memory>

std::unique_ptr<int> ptr = std::make_unique<int>(42);
// Automatic cleanup when ptr goes out of scope

std::unique_ptr<int[]> arr = std::make_unique<int[]>(10);
// Array version
```

### shared_ptr - Shared ownership
```cpp
std::shared_ptr<int> ptr1 = std::make_shared<int>(42);
std::shared_ptr<int> ptr2 = ptr1;  // Reference count = 2
// Cleanup when last shared_ptr is destroyed
```

### weak_ptr - Breaking cycles
```cpp
std::weak_ptr<int> weak = ptr1;
if (auto locked = weak.lock()) {
    // Use locked (shared_ptr) temporarily
}
```

## RAII Pattern

```cpp
class Resource {
    int *data;
public:
    Resource(size_t size) : data(new int[size]) {}
    ~Resource() { delete[] data; }  // Automatic cleanup
    
    // Rule of 5
    Resource(const Resource& other);
    Resource& operator=(const Resource& other);
    Resource(Resource&& other) noexcept;
    Resource& operator=(Resource&& other) noexcept;
};
```

## Move Semantics

```cpp
std::vector<int> v1(1000);
std::vector<int> v2 = std::move(v1);  // Transfers ownership
// v1 is in valid but unspecified state

// Perfect forwarding
template<typename T>
void wrapper(T&& arg) {
    func(std::forward<T>(arg));  // Preserves lvalue/rvalue
}
```

## Modern Best Practices

- Prefer `std::make_unique` and `std::make_shared` over raw `new`
- Use smart pointers, avoid raw `new`/`delete`
- Follow Rule of Zero: use standard library types when possible
- Use `std::vector` instead of C-style arrays
- Enable compiler warnings and sanitizers