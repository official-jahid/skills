---
name: windows-api
description: Windows API patterns - kernel32, user32, gdi32 functions, handles, and system integration.
license: MIT
metadata:
  author: cpp-skills
  version: "1.0.0"
---

# Windows API (C++)

Essential Windows API patterns for system programming.

## Handle Management

```cpp
// Create and close handles properly
HANDLE hFile = CreateFile(L"file.txt", GENERIC_READ, 0, NULL, OPEN_EXISTING, 0, NULL);
if (hFile != INVALID_HANDLE_VALUE) {
    // Use handle
    CloseHandle(hFile);
}

// RAII wrapper for handles
class Handle {
    HANDLE h;
public:
    Handle(HANDLE handle) : h(handle) {}
    ~Handle() { if (h && h != INVALID_HANDLE_VALUE) CloseHandle(h); }
    HANDLE get() const { return h; }
};
```

## File Operations

```cpp
// Read file
HANDLE hFile = CreateFile(L"file.txt", GENERIC_READ, FILE_SHARE_READ, 
                         NULL, OPEN_EXISTING, 0, NULL);

DWORD size = GetFileSize(hFile, NULL);
BYTE* buffer = new BYTE[size];
DWORD bytesRead;
ReadFile(hFile, buffer, size, &bytesRead, NULL);

CloseHandle(hFile);
```

## Registry Operations

```cpp
// Write registry value
HKEY hKey;
RegCreateKeyEx(HKEY_CURRENT_USER, L"Software\\MyApp", 0, NULL, 
               REG_CREATED_NEW_KEY, KEY_WRITE, NULL, &hKey, NULL);
RegSetValueEx(hKey, L"Setting", 0, REG_SZ, (BYTE*)"value", 12);
RegCloseKey(hKey);
```

## Window Creation

```cpp
// Create window
WNDCLASS wc = {};
wc.lpfnWndProc = DefWindowProc;
wc.lpszClassName = L"MyClass";
RegisterClass(&wc);

HWND hwnd = CreateWindow(wc.lpszClassName, L"My Window", 
                         WS_OVERLAPPEDWINDOW, 0, 0, 800, 600,
                         NULL, NULL, GetModuleHandle(NULL), NULL);
ShowWindow(hwnd, SW_SHOW);
```

## Thread Creation

```cpp
DWORD WINAPI ThreadProc(LPVOID param) {
    // Thread work
    return 0;
}

HANDLE hThread = CreateThread(NULL, 0, ThreadProc, NULL, 0, NULL);
WaitForSingleObject(hThread, INFINITE);
CloseHandle(hThread);
```

## Module Management

```cpp
// Load DLL
HMODULE hDll = LoadLibrary(L"mydll.dll");
if (hDll) {
    typedef int (*MyFunc)();
    MyFunc func = (MyFunc)GetProcAddress(hDll, "MyFunction");
    if (func) func();
    FreeLibrary(hDll);
}
```

## Error Handling

```cpp
void CheckLastError() {
    DWORD error = GetLastError();
    if (error != ERROR_SUCCESS) {
        LPWSTR messageBuffer = NULL;
        FormatMessage(FORMAT_MESSAGE_ALLOCATE_BUFFER | FORMAT_MESSAGE_FROM_SYSTEM,
                      NULL, error, 0, (LPWSTR)&messageBuffer, 0, NULL);
        wprintf(L"Error: %ls\n", messageBuffer);
        LocalFree(messageBuffer);
    }
}
```