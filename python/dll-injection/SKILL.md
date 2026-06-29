---
name: dll-injection
description: Windows DLL injection patterns using pyinjector library. Covers process attachment, injection methods, and PyInstaller build considerations.
license: MIT
metadata:
  author: python-skills
  version: "1.0.0"
---

# DLL Injection for Windows

Skill for injecting DLLs into Windows processes using pyinjector.

## Installation

```bash
pip install pyinjector
```

## Basic Injection

```python
from pyinjector import inject

# Inject DLL into process by PID
inject(process_id, "path/to/dll.dll")
```

## With Pymem for Process Access

```python
import pymem
from pyinjector import inject

# Get process
pm = pymem.Pymem("target.exe")
pid = pm.process_id

# Inject DLL
inject(pid, "dlls/cheat.dll")
```

## Injection Helper Function

```python
import os
import sys

def get_resource_path(relative_path):
    """Get path to resource for both dev and PyInstaller"""
    try:
        base_path = sys._MEIPASS
    except Exception:
        base_path = os.path.abspath(".")
    return os.path.join(base_path, relative_path)

# Usage in Flask endpoint
@app.post('/inject-feature')
def injectFeature():
    pid = get_pid('target.exe')
    if pid:
        try:
            inject(pid, get_resource_path('dlls/feature.dll'))
            return jsonify(status=200, message="Injected")
        except Exception as e:
            return jsonify(status=500, message=str(e))
```

## Flask Integration Example

```python
@app.post('/feature-on')
def featureOn():
    try:
        pid = Memory.get_pid('target.exe')
        inject(pid, get_resource_path('dlls/feature.dll'))
        return jsonify(status=200)
    except:
        return jsonify(status=305)  # Injection failed
```

## Common Patterns

### Conditional Injection
```python
def check_and_inject(process_name, dll_name):
    if check_process(process_name):
        pm = pymem.Pymem(process_name)
        inject(pm.process_id, get_resource_path(f'dlls/{dll_name}'))
        return True
    return False
```

### Handle Injection Errors
```python
try:
    inject(pid, dll_path)
except Exception as e:
    print(f"Injection failed: {e}")
    # Log error or notify user
```

## Security Requirements

- Run as Administrator
- Antivirus may flag injected processes
- Some anticheats block injection
- Use only on software you own

## DLL Path for PyInstaller Builds

Always use `get_resource_path()` when building executables:

```python
# Wrong - won't work in compiled exe
inject(pid, "dlls/cheat.dll")

# Correct - works in both dev and compiled
inject(pid, get_resource_path('dlls/cheat.dll'))
```