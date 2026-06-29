---
name: process-memory
description: Process memory manipulation using pymem - ReadProcessMemory, WriteProcessMemory, AOB scanning, and memory monitoring.
license: MIT
metadata:
  author: python-skills
  version: "1.0.0"
---

# Process Memory (Python)

Windows process memory manipulation using pymem.

## Process Attachment

```python
import pymem

# Attach to process
pm = pymem.Pymem("process.exe")
# Or by PID
# pm = pymem.Pymem(pid=process_id)

# Get process info
pid = pm.process_id
handle = pm.process_handle
```

## ReadProcessMemory

```python
# Read bytes
data = pm.read_bytes(address, length)

# Read integer
value = pm.read_int(address)

# Read pointer chain
def read_pointer_chain(pm, base, offsets):
    addr = pm.read_int(base)
    for offset in offsets[:-1]:
        addr = pm.read_int(addr + offset)
    return pm.read_int(addr + offsets[-1])
```

## WriteProcessMemory

```python
# Write bytes
pm.write_bytes(address, new_bytes, length)

# Write integer
pm.write_int(address, value)
```

## AOB Scanning

```python
from pymem.pattern import pattern_scan_module

def scan_pattern(pm, module_name, pattern_bytes):
    addr = pattern_scan_module(pm.process_handle, module_name, pattern_bytes)
    return pm.read_int(addr) + offset
```

## Pattern Conversion

```python
def pattern_to_bytes(pattern):
    """Convert AOB string to bytes"""
    bytes_list = []
    for part in pattern.split(' '):
        if part == '??':
            bytes_list.append('?')
        else:
            bytes_list.append(int(part, 16))
    return bytes(bytes_list)
```

## Memory Monitoring

```python
import psutil

def monitor_process(pid):
    process = psutil.Process(pid)
    while True:
        mem = process.memory_info()
        print(f"RSS: {mem.rss / 1024 / 1024:.2f} MB")
        print(f"VMS: {mem.vms / 1024 / 1024:.2f} MB")
        sleep(1)
```

## Privilege Escalation

```python
import ctypes

def enable_debug_privilege():
    PROCESS_ALL_ACCESS = 0x1F0FFF
    token = ctypes.windll.kernel32.OpenProcessToken(
        ctypes.windll.kernel32.GetCurrentProcess(),
        0x28,
        ctypes.byref(ctypes.c_void_p())
    )
    # Adjust privileges for process access
```

## Virtual Memory

```python
# Query memory region
mbi = pymem.memory_virtual_query(address)
print(f"Base: {hex(mbi.BaseAddress)}")
print(f"Region size: {mbi.RegionSize}")

# Allocate memory
allocated = pm.allocate(size)
```