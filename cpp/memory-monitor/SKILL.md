---
name: memory-monitor
description: Memory monitoring patterns - Performance counters, memory usage tracking, and real-time monitoring in C++.
license: MIT
metadata:
  author: cpp-skills
  version: "1.0.0"
---

# Memory Monitor (C++)

Memory monitoring using Windows Performance Counters and API.

## Process Memory Info

```cpp
#include <psapi.h>

PROCESS_MEMORY_COUNTERS GetProcessMemory(HANDLE hProcess) {
    PROCESS_MEMORY_COUNTERS pmc;
    GetProcessMemoryInfo(hProcess, &pmc, sizeof(pmc));
    return pmc;
}

void PrintMemoryUsage(HANDLE hProcess) {
    auto pmc = GetProcessMemory(hProcess);
    printf("Working Set: %lu KB\n", pmc.WorkingSetSize / 1024);
    printf("Peak Working Set: %lu KB\n", pmc.PeakWorkingSetSize / 1024);
    printf("Page Fault Count: %lu\n", pmc.PageFaultCount);
}
```

## System Memory Info

```cpp
#include <windows.h>

void GetSystemMemoryStatus() {
    MEMORYSTATUSEX memInfo;
    memInfo.dwLength = sizeof(memInfo);
    GlobalMemoryStatusEx(&memInfo);
    
    printf("Total Physical: %llu MB\n", memInfo.ullTotalPhys / (1024 * 1024));
    printf("Available Physical: %llu MB\n", memInfo.ullAvailPhys / (1024 * 1024));
    printf("Memory Load: %lu%%\n", memInfo.dwMemoryLoadPercent);
}
```

## Real-time Monitoring

```cpp
class MemoryMonitor {
    HANDLE hProcess;
    std::thread monitorThread;
    bool running = true;
    
public:
    MemoryMonitor(DWORD pid) : hProcess(OpenProcess(PROCESS_QUERY_INFORMATION, FALSE, pid)) {}
    
    void Start() {
        monitorThread = std::thread([this]() {
            while (running) {
                auto pmc = GetProcessMemory(hProcess);
                printf("[%lu] RAM: %llu KB\n", GetTickCount(), pmc.WorkingSetSize / 1024);
                Sleep(1000);
            }
        });
    }
    
    void Stop() {
        running = false;
        if (monitorThread.joinable())
            monitorThread.join();
    }
};
```

## Performance Counters

```cpp
#include <pdh.h>

class PerfCounter {
    PDH_HQUERY query;
    PDH_HCOUNTER counter;
    
public:
    PerfCounter(const std::wstring& counterPath) {
        PdhOpenQuery(NULL, 0, &query);
        PdhAddCounter(query, counterPath.c_str(), 0, &counter);
        PdhCollectQueryData(query);
    }
    
    double GetValue() {
        PdhCollectQueryData(query);
        PDH_FMT_COUNTERVALUE value;
        PdhGetFormattedCounterValue(counter, PDH_FMT_DOUBLE, NULL, &value);
        return value.doubleValue;
    }
};
```

## Memory Leak Detection

```cpp
#ifdef _DEBUG
#define CRTDBG_MAP_ALLOC
#include <crtdbg.h>

int main() {
    _CrtSetDbgFlag(_CRTDBG_ALLOC_MEM_DF | _CRTDBG_LEAK_CHECK_DF);
    // ... code ...
    return 0;
}
#endif
```