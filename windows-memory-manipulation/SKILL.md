# Windows Memory Manipulation with PyMem

Skill for Windows process memory scanning and manipulation using PyMem library.

## Installation

```bash
pip install Pymem
```

## Basic Process Access

```python
import pymem

# Attach to process
pm = pymem.Pymem("process.exe")

# Get process handle and PID
process_handle = pm.process_handle
process_id = pm.process_id
```

## Pattern Scanning

```python
from pymem.pattern import pattern_scan_all

def find_pattern(pm, module_name, pattern):
    if isinstance(pattern, str):
        pattern = mkp(pattern)  # Convert AOB string to bytes
    return pattern_scan_all(pm.process_handle, pattern, return_multiple=True)

# Example AOB pattern
pattern = "FF FF 00 00 00 00 00 00"
addresses = find_pattern(pm, "process.exe", pattern)
```

## AOB String to Bytes Conversion

```python
def mkp(aob: str) -> bytes:
    """Convert AOB pattern string to bytes"""
    if '??' in aob:
        if aob.startswith("??"):
            aob = f" {aob}"
            n = aob.replace(" ??", ".").replace(" ", "\\x")
        else:
            n = aob.replace(" ??", ".").replace(" ", "\\x")
        return bytes(n.encode())
    else:
        m = aob.replace(" ", "\\x")
        return bytes(f"\\x{m}".encode())
```

## Memory Read/Write

```python
# Read bytes
data = pm.read_bytes(address, length)

# Write bytes
pm.write_bytes(address, new_bytes, length)

# Read integer
value = pm.read_int(address)

# Write integer
pm.write_int(address, value)
```

## Process Utilities

```python
def get_process(processName):
    """Check if process is running and return PID"""
    try:
        pm = pymem.Pymem(processName)
        return pm.process_id
    except:
        return False

def get_pid(processName):
    """Get process ID for running process"""
    pm = pymem.Pymem(processName)
    return pm.process_id
```

## Privilege Adjustment (Required for Some Operations)

```python
import ctypes

def adjust_privileges():
    SE_DEBUG_NAME = "SeDebugPrivilege"
    SE_PRIVILEGE_ENABLED = 0x00000002
    token_handle = ctypes.c_void_p()
    luid = ctypes.c_longlong()
    
    ctypes.windll.advapi32.OpenProcessToken(
        ctypes.windll.kernel32.GetCurrentProcess(),
        0x20 | 0x8,
        ctypes.byref(token_handle)
    )
    ctypes.windll.advapi32.LookupPrivilegeValueA(
        0, SE_DEBUG_NAME.encode('ascii'), ctypes.byref(luid)
    )
    
    class LUID_AND_ATTRIBUTES(ctypes.Structure):
        _fields_ = [("Luid", ctypes.c_longlong), ("Attributes", ctypes.c_ulong)]
    
    class TOKEN_PRIVILEGES(ctypes.Structure):
        _fields_ = [("PrivilegeCount", ctypes.c_ulong), ("Privileges", LUID_AND_ATTRIBUTES)]
    
    new_privileges = TOKEN_PRIVILEGES(1, LUID_AND_ATTRIBUTES(luid.value, SE_PRIVILEGE_ENABLED))
    ctypes.windll.advapi32.AdjustTokenPrivileges(
        token_handle, False, ctypes.byref(new_privileges), 0, None, None
    )
    ctypes.windll.kernel32.CloseHandle(token_handle)
```

## Scan and Replace Pattern

```python
def scan_and_replace(processName, search, replace):
    try:
        pm = pymem.Pymem(processName)
        if isinstance(search, str):
            search = mkp(search)
        matches = pattern_scan_all(pm.process_handle, search, return_multiple=True)
        if matches:
            for match in matches:
                pm.write_bytes(match, replace, len(replace))
            return True
        return False
    except Exception as e:
        print(f"Error: {e}")
        return False
```

## Requirements

- Windows OS only
- Python 3.x
- Process must be running before attachment
- Administrator privileges recommended