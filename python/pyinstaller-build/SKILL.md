---
name: pyinstaller-build
description: Building Python applications into Windows executables using PyInstaller. Covers single-file builds, resource paths, and troubleshooting.
license: MIT
metadata:
  author: python-skills
  version: "1.0.0"
---

# PyInstaller Build for Python Applications

Skill for building Python applications into Windows executables using PyInstaller.

## Installation

```bash
pip install pyinstaller
```

## Basic Build Commands

### Single File Executable
```bash
pyinstaller --onefile app.py
```

### Windowed/GUI Application (no console)
```bash
pyinstaller --onefile --windowed app.py
```

### With Custom Icon
```bash
pyinstaller --onefile --windowed --icon=logo.ico app.py
```

## Including Additional Files

### Include Data Files
```bash
pyinstaller --onefile --add-data "templates;templates" --add-data "static;static" --add-data "dlls;dlls" app.py
```

### Include Specific DLLs
```bash
pyinstaller --onefile --add-binary "dlls/cheat.dll;dlls" app.py
```

## Build Options

| Option | Description |
|--------|-------------|
| `--onefile` | Bundles everything into a single executable |
| `--windowed` | Windows: no console window; macOS: no terminal window |
| `--noconsole` | Alternative to --windowed for hiding console |
| `--icon=ICON` | Sets the application icon (.ico/.icns) |
| `--add-data` | Include data files (source;destination) |
| `--add-binary` | Include binary files |
| `--name=NAME` | Set output executable name |
| `--hidden-import` | Force include hidden imports |

## Handling Resource Paths

When building with PyInstaller, file paths change. Use this pattern:

```python
import sys
import os

def get_resource_path(relative_path):
    """ Get absolute path to resource, works for dev and PyInstaller """
    try:
        base_path = sys._MEIPASS
    except Exception:
        base_path = os.path.abspath(".")
    return os.path.join(base_path, relative_path)

# Usage
dll_path = get_resource_path('dlls/cheat.dll')
```

## Common Build Patterns

### Flask Applications
```bash
pyinstaller --onefile --windowed --add-data "templates;templates" --add-data "static;static" app.py
```

### Applications with Config Files
```bash
pyinstaller --onefile --add-data "config.json;." app.py
```

### Multi-file Projects
```bash
pyinstaller --onefile --add-data "src;src" app.py
```

## Troubleshooting

### Missing Module Error
```bash
pyinstaller --hidden-import=module_name app.py
```

### DLL Not Found
Make sure to include DLLs with `--add-binary` and use `get_resource_path()` function.

### Large Executable Size
Use `--onefile` and avoid including unnecessary dependencies.