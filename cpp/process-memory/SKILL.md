---
name: process-memory
description: Process memory patterns - ReadProcessMemory, WriteProcessMemory, memory scanning, and Windows API integration.
license: MIT
metadata:
  author: cpp-skills
  version: "1.0.0"
---

# Process Memory (C++)

Windows process memory manipulation using Windows API.

## ReadProcessMemory

```cpp
#include <windows.h>
#include <tlhelp32.h>

DWORD FindProcessId(const std::string& processName) {
    HANDLE hSnapshot = CreateToolhelp32Snapshot(TH32CS_SNAPPROCESS, 0);
    PROCESSENTRY32 pe;
    pe.dwSize = sizeof(pe);
    
    if (Process32First(hSnapshot, &pe)) {
        do {
            if (processName == pe.szExeFile) {
                CloseHandle(hSnapshot);
                return pe.th32ProcessID;
            }
        } while (Process32Next(hSnapshot, &pe));
    }
    CloseHandle(hSnapshot);
    return 0;
}

bool ReadMemory(HANDLE hProcess, DWORD address, void* buffer, SIZE_T size) {
    SIZE_T bytesRead;
    return ReadProcessMemory(hProcess, (LPCVOID)address, buffer, size, &bytesRead);
}
```

## WriteProcessMemory

```cpp
bool WriteMemory(HANDLE hProcess, DWORD address, const void* buffer, SIZE_T size) {
    SIZE_T bytesWritten;
    return WriteProcessMemory(hProcess, (LPVOID)address, buffer, size, &bytesWritten);
}
```

## OpenProcess and Privileges

```cpp
HANDLE OpenProcessWithPrivileges(DWORD pid) {
    HANDLE hProcess = OpenProcess(PROCESS_ALL_ACCESS, FALSE, pid);
    
    // Enable SeDebugPrivilege
    HANDLE hToken;
    TOKEN_PRIVILEGES tp;
    if (OpenProcessToken(GetCurrentProcess(), TOKEN_ADJUST_PRIVILEGES, &hToken)) {
        tp.PrivilegeCount = 1;
        tp.Privileges[0].Attributes = SE_PRIVILEGE_ENABLED;
        LookupPrivilegeValue(NULL, SE_DEBUG_NAME, &tp.Privileges[0].Luid);
        AdjustTokenPrivileges(hToken, FALSE, &tp, 0, NULL, NULL);
        CloseHandle(hToken);
    }
    
    return hProcess;
}
```

## VirtualAllocEx

```cpp
LPVOID AllocateRemoteMemory(HANDLE hProcess, SIZE_T size) {
    return VirtualAllocEx(
        hProcess,
        NULL,
        size,
        MEM_COMMIT | MEM_RESERVE,
        PAGE_EXECUTE_READWRITE
    );
}
```

## VirtualQueryEx

```cpp
MEMORY_BASIC_INFORMATION QueryMemory(HANDLE hProcess, DWORD address) {
    MEMORY_BASIC_INFORMATION mbi;
    VirtualQueryEx(hProcess, (LPVOID)address, &mbi, sizeof(mbi));
    return mbi;
}
```

## AOB Scan

```cpp
bool ComparePattern(const BYTE* data, const BYTE* pattern, const char* mask) {
    for (size_t i = 0; i < strlen(mask); i++) {
        if (mask[i] == 'x' && data[i] != pattern[i])
            return false;
    }
    return true;
}

DWORD FindPattern(HANDLE hProcess, DWORD start, DWORD size, 
                  const BYTE* pattern, const char* mask) {
    BYTE* buffer = new BYTE[size];
    SIZE_T bytesRead;
    ReadProcessMemory(hProcess, (LPCVOID)start, buffer, size, &bytesRead);
    
    for (DWORD i = 0; i < size; i++) {
        if (ComparePattern(buffer + i, pattern, mask)) {
            delete[] buffer;
            return start + i;
        }
    }
    delete[] buffer;
    return 0;
}
```

## Thread Management

```cpp
HANDLE CreateRemoteThread(HANDLE hProcess, LPVOID address) {
    return CreateRemoteThread(
        hProcess,
        NULL,
        0,
        (LPTHREAD_START_ROUTINE)address,
        NULL,
        0,
        NULL
    );
}
```