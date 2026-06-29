---
name: terminal-app
description: Terminal application patterns - CLI parsing, output formatting, progress bars, and user interaction.
license: MIT
metadata:
  author: python-skills
  version: "1.0.0"
---

# Terminal Application (Python)

Build CLI applications with Python.

## Argument Parsing

```python
import argparse

parser = argparse.ArgumentParser(description='My CLI Tool')
parser.add_argument('input', help='Input file')
parser.add_argument('-o', '--output', help='Output file')
parser.add_argument('-v', '--verbose', action='store_true')
parser.add_argument('-n', '--number', type=int, default=10)

args = parser.parse_args()
```

## Rich Output

```python
from rich.console import Console
from rich.table import Table
from rich.progress import Progress

console = Console()

# Tables
table = Table(title="Users")
table.add_column("Name", style="cyan")
table.add_column("Age", style="magenta")
table.add_row("John", "30")
console.print(table)

# Progress bars
with Progress() as progress:
    task = progress.add_task("[cyan]Processing...", total=100)
    while not progress.finished:
        progress.update(task, advance=10)
```

## Interactive Prompts

```python
import questionary

def prompt_user():
    name = questionary.text("Your name").ask()
    choice = questionary.select(
        "Pick a color",
        choices=["Red", "Green", "Blue"]
    ).ask()
    confirm = questionary.confirm("Continue?").ask()
    return {"name": name, "choice": choice}
```

## Colors and Styling

```python
class Colors:
    RED = '\033[91m'
    GREEN = '\033[92m'
    YELLOW = '\033[93m'
    RESET = '\033[0m'

def print_success(msg):
    print(f"{Colors.GREEN}✓ {msg}{Colors.RESET}")

def print_error(msg):
    print(f"{Colors.RED}✗ {msg}{Colors.RESET}")
```

## Keyboard Input

```python
import msvcrt
import sys

def get_key():
    if msvcrt.kbhit():
        return msvcrt.getch()
    return None

# Or cross-platform with keyboard module
# import keyboard
# keyboard.on_press_key('space', lambda: print('Space pressed'))
```

## Menu System

```python
def show_menu():
    print("\n1. Create user")
    print("2. Delete user")
    print("3. Exit")
    
    choice = input("Select option: ")
    return int(choice) if choice.isdigit() else 0

while True:
    choice = show_menu()
    if choice == 3:
        break
    # Handle choice
```