# 🐍 Python 3.13 Developer Pocketbook

A concise, idiomatic, production-ready **cheatsheet** for Python developers.  
Reading time: ~35 min.  

---

# Table of Contents  
- [1. Overview](#1-overview)  
- [2. Setup](#2-setup)  
- [3. Fundamentals](#3-fundamentals)  
- [4. Data Structures](#4-data-structures)  
- [5. Functions & Paradigm](#5-functions--paradigm)  
- [6. Error Handling & Debugging](#6-error-handling--debugging)  
- [7. Code Organization](#7-code-organization)  
- [8. Complete Example Program](#8-complete-example-program)  
- [9. Best Practices](#9-best-practices)  
- [10. Quick Reference](#10-quick-reference)  

---

## 1. Overview  
- **Language**: Python 3.13 (high-level, dynamic, multi-paradigm)  
- **Philosophy**: "Readability counts." (see [PEP 20 – Zen of Python](https://peps.python.org/pep-0020/))  
- **Paradigm**: Imperative, OOP, functional, concurrent, scripting  
- **Use cases**: Web, data science, automation, AI/ML, system scripting  
- **Learning curve**: Gentle → Expert depth  

---

## 2. Setup  

### Install  
```bash
# Official installer: https://www.python.org/downloads/
python3 --version
```  

### Package Management  
```bash
# Built-in
pip install requests
pip list
pip install -r requirements.txt
```  

### Editors  
- **VS Code** (Python extension, Pylance, Black formatter)  
- **PyCharm** (robust refactoring/debugging)  

---

## 3. Fundamentals  

### Variables  
```python
x = 42          # dynamic typing
PI = 3.14159    # constant by convention
```  

### Types  
```python
int, float, bool, str, bytes, list, tuple, set, dict, None
```  

### Conversions  
```python
int("5")      # 5
str(42)       # "42"
list("abc")   # ['a','b','c']
```  

### Operators  
```python
+ - * / // % **
== != < > <= >=
and or not
in, not in, is, is not
```  

### Control Flow  
```python
if x > 0:
    print("Positive")
elif x == 0:
    pass
else:
    print("Negative")

for i in range(3): print(i)
while cond: break

match value:  # Python 3.10+
    case 1: print("one")
    case _: print("default")
```  

### I/O  
```python
name = input("Name: ")
with open("file.txt") as f:
    data = f.read()
```  

---

## 4. Data Structures  

### Lists  
```python
nums = [1,2,3]
nums.append(4)
squares = [x*x for x in nums]
```  

### Dicts  
```python
user = {"id": 1, "name": "Ada"}
user.get("email", "N/A")
```  

### Sets  
```python
unique = {1,2,3}
unique.add(2)
```  

### Tuples  
```python
point = (3,4)
x, y = point
```  

---

## 5. Functions & Paradigm  

### Functions  
```python
def greet(name: str = "World") -> str:
    return f"Hello {name}"
```  

### Lambdas & Closures  
```python
add = lambda x,y: x+y
def make_counter():
    count = 0
    def inc():
        nonlocal count
        count += 1
        return count
    return inc
```  

### OOP  
```python
class Animal:
    def speak(self): return "..."

class Dog(Animal):
    def speak(self): return "Woof"
```  

### Generators & Async  
```python
def gen():
    yield 1; yield 2

import asyncio
async def task(): await asyncio.sleep(1)
```  

---

## 6. Error Handling & Debugging  

```python
try:
    risky()
except ValueError as e:
    print(e)
finally:
    cleanup()
```  

### Logging  
```python
import logging
logging.basicConfig(level=logging.INFO)
logging.info("Started")
```  

### Testing  
```python
# test_example.py
def add(x,y): return x+y

def test_add():
    assert add(2,3) == 5
```  
Run: `pytest`  

---

## 7. Code Organization  

- **Modules**: any `.py` file  
- **Packages**: folder with `__init__.py`  
- **Imports**:  
```python
from math import sqrt
import requests as r
```  

### Project Layout  
```
project/
  ├── src/
  │    └── app.py
  ├── tests/
  │    └── test_app.py
  ├── requirements.txt
  └── pyproject.toml
```  

---

## 8. Complete Example Program  

**Task Manager CLI**  

```python
# task_manager.py
import json, os

FILE = "tasks.json"

def load():
    return json.load(open(FILE)) if os.path.exists(FILE) else []

def save(tasks):
    json.dump(tasks, open(FILE,"w"), indent=2)

def add_task(title, priority=1):
    tasks = load()
    tasks.append({"title": title, "priority": priority})
    save(tasks)

def list_tasks():
    for i,t in enumerate(load(),1):
        print(f"{i}. {t['title']} (p={t['priority']})")

if __name__ == "__main__":
    import argparse
    p = argparse.ArgumentParser()
    p.add_argument("cmd", choices=["add","list"])
    p.add_argument("title", nargs="?")
    p.add_argument("-p","--priority", type=int, default=1)
    a = p.parse_args()

    if a.cmd=="add" and a.title:
        add_task(a.title, a.priority)
    elif a.cmd=="list":
        list_tasks()
```  

### Test  
```python
# test_task.py
import task_manager as tm

def test_add_and_list(tmp_path, monkeypatch):
    file = tmp_path / "tasks.json"
    monkeypatch.setattr(tm, "FILE", str(file))

    tm.add_task("demo", 2)
    assert tm.load()[0]["priority"] == 2
```  

Run: `pytest -q`  

---

## 9. Best Practices  

- Style: [PEP8](https://peps.python.org/pep-0008/) + type hints  
- Use `black`, `ruff`, `mypy` for formatting/linting/types  
- Small functions, descriptive names, docstrings  
- Avoid premature optimization → profile when needed  
- Handle secrets via env vars (`os.environ`)  
- Libraries: `requests`, `pydantic`, `sqlalchemy`, `pytest`, `fastapi`  

---

## 10. Quick Reference  

### Syntax  
```python
[f"{x}" for x in range(3) if x%2==0]  # list comprehension
{n for n in range(5)}                 # set comprehension
{k:v for k,v in enumerate("abc")}     # dict comprehension
```  

### Standard Library Highlights  
- `pathlib` – filesystem  
- `itertools` – iterators  
- `functools` – caching, partials  
- `datetime`, `zoneinfo`  
- `subprocess` – run commands  

### CLI  
```bash
python -m venv .venv
source .venv/bin/activate
python -m pip install -U pip
python -m unittest
```  

---

📚 References:  
- [Python Docs](https://docs.python.org/3/)  
- [PEP 8](https://peps.python.org/pep-0008/)  
- [pytest](https://docs.pytest.org/)  
- [AsyncIO](https://docs.python.org/3/library/asyncio.html)  
