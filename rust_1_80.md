# 🦀 Rust 1.80 Pocketbook / Cheatsheet

> Concise yet comprehensive reference for modern, idiomatic Rust.  
> Target: Setup → Build → Deploy production-quality Rust apps.  
> Sources: [The Rust Book](https://doc.rust-lang.org/book/), [Rust Reference](https://doc.rust-lang.org/reference/), [Cargo](https://doc.rust-lang.org/cargo/).

---

## 📑 Table of Contents
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

- **Language**: Rust 1.80  
- **Paradigm**: Multi-paradigm (functional + imperative + data-oriented)  
- **Philosophy**: *Memory safety, zero-cost abstractions, fearless concurrency.*  
- **Use cases**: Systems programming, CLIs, web services, embedded, WASM, performance-critical apps.  
- **Learning curve**: Steep at first (ownership/borrowing), but stable & rewarding.

---

## 2. Setup

### Install
```bash
# Install via rustup (recommended)
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
rustc --version   # Verify installation
```

### Package Manager (Cargo)
```bash
cargo new my_project     # Create new project
cargo build              # Build debug
cargo run                # Run
cargo build --release    # Optimized build
cargo test               # Run tests
cargo fmt                # Auto-format
cargo clippy             # Lint
```

### Editors
- **VS Code**: `rust-analyzer`, `CodeLLDB`, `crates`  
- **JetBrains CLion/IntelliJ**: Rust plugin  
- **Neovim**: `rust-analyzer` via LSP

---

## 3. Fundamentals

### Variables & Constants
```rust
let x = 5;           // immutable
let mut y = 10;      // mutable
const MAX: u32 = 100; // compile-time constant
static GLOBAL: &str = "hi"; // global static
```

### Types & Conversions
```rust
let a: i32 = 42;
let b: f64 = a as f64; // explicit cast
```

### Operators
- Arithmetic: `+ - * / %`
- Comparison: `== != < <= > >=`
- Logical: `&& || !`

### Control Flow
```rust
if x > 0 { println!("positive"); }
for i in 0..5 { println!("{}", i); } // 0..4
while x > 0 { x -= 1; }
match x {
    0 => println!("zero"),
    1..=9 => println!("small"),
    _ => println!("other"),
}
```

### I/O
```rust
use std::io::{self, Write};
let mut input = String::new();
io::stdin().read_line(&mut input)?;
println!("You typed: {}", input.trim());
```

---

## 4. Data Structures

### Built-ins
```rust
let arr = [1, 2, 3];
let mut vec = vec![1, 2, 3];
vec.push(4);

use std::collections::{HashMap, HashSet};
let mut map = HashMap::new();
map.insert("a", 1);
let mut set: HashSet<_> = vec![1,2,3].into_iter().collect();
```

### Language-specific
```rust
// Tuple
let tup: (i32, &str) = (42, "hi");
let (a, b) = tup;

// Struct
struct Point { x: i32, y: i32 }
let p = Point { x: 1, y: 2 };

// Enum
enum Direction { North, East, South, West }
```

---

## 5. Functions & Paradigm

```rust
fn add(a: i32, b: i32) -> i32 { a + b }

// Closures
let square = |x: i32| x * x;

// Higher-order
fn apply<F: Fn(i32) -> i32>(f: F, x: i32) -> i32 { f(x) }

// Generics & Traits
fn largest<T: Ord>(list: &[T]) -> &T {
    list.iter().max().unwrap()
}
```

Rust combines **functional programming** (iterators, pattern matching, immutability) with **OOP-like traits** and **safe concurrency**.

---

## 6. Error Handling & Debugging

### Results & Options
```rust
fn divide(a: f64, b: f64) -> Option<f64> {
    if b == 0.0 { None } else { Some(a/b) }
}

fn read_file(path: &str) -> std::io::Result<String> {
    std::fs::read_to_string(path)
}
```

### Error Propagation
```rust
fn run() -> Result<(), Box<dyn std::error::Error>> {
    let content = std::fs::read_to_string("file.txt")?;
    println!("{}", content);
    Ok(())
}
```

### Debugging & Testing
```rust
dbg!(x);  // print debug info

#[cfg(test)]
mod tests {
    #[test]
    fn test_add() {
        assert_eq!(2+2, 4);
    }
}
```

---

## 7. Code Organization

- **Modules**
```rust
mod utils;        // in utils.rs
pub mod api;      // expose module
use crate::api::*;
```

- **Project layout**
```
Cargo.toml
src/
 ├─ main.rs
 ├─ lib.rs
 ├─ utils.rs
 └─ api/mod.rs
```

- **Dependencies** (Cargo.toml)
```toml
[dependencies]
serde = { version = "1", features = ["derive"] }
tokio = { version = "1", features = ["full"] }
```

---

## 8. Complete Example Program

**Task Manager CLI**  

```rust
// src/main.rs
use std::fs::{OpenOptions};
use std::io::{self, Write, BufRead, BufReader};

#[derive(Debug)]
struct Task {
    id: usize,
    title: String,
    priority: u8,
}

fn load_tasks(path: &str) -> io::Result<Vec<Task>> {
    let file = OpenOptions::new().read(true).create(true).open(path)?;
    let reader = BufReader::new(file);
    let mut tasks = Vec::new();
    for (i, line) in reader.lines().enumerate() {
        if let Ok(l) = line {
            let parts: Vec<_> = l.split('|').collect();
            if parts.len() == 2 {
                tasks.push(Task {
                    id: i,
                    title: parts[0].to_string(),
                    priority: parts[1].parse().unwrap_or(1),
                });
            }
        }
    }
    Ok(tasks)
}

fn save_tasks(path: &str, tasks: &[Task]) -> io::Result<()> {
    let mut file = OpenOptions::new().write(true).truncate(true).create(true).open(path)?;
    for t in tasks {
        writeln!(file, "{}|{}", t.title, t.priority)?;
    }
    Ok(())
}

fn main() -> io::Result<()> {
    let path = "tasks.txt";
    let mut tasks = load_tasks(path)?;

    println!("Commands: add <title> <priority>, list, quit");
    let stdin = io::stdin();
    for line in stdin.lock().lines() {
        let input = line?;
        let parts: Vec<_> = input.split_whitespace().collect();
        if parts.is_empty() { continue; }

        match parts[0] {
            "add" if parts.len() >= 3 => {
                let title = parts[1].to_string();
                let priority = parts[2].parse().unwrap_or(1);
                let id = tasks.len();
                tasks.push(Task { id, title, priority });
                save_tasks(path, &tasks)?;
                println!("Task added!");
            }
            "list" => {
                for t in &tasks {
                    println!("[{}] {} (p{})", t.id, t.title, t.priority);
                }
            }
            "quit" => break,
            _ => println!("Unknown command"),
        }
    }
    Ok(())
}
```

**Test (in `tests/basic.rs`)**
```rust
#[test]
fn simple_math() {
    assert_eq!(2 + 2, 4);
}
```

Run with:
```bash
cargo run
cargo test
```

---

## 9. Best Practices

- **Style**: Follow [Rust API Guidelines](https://rust-lang.github.io/api-guidelines/) & [Rustfmt](https://github.com/rust-lang/rustfmt)  
- **Readability**: Prefer immutability, descriptive names, small functions  
- **Performance**: Use iterators, avoid unnecessary clones, leverage `&` over copies  
- **Security**: Avoid `unsafe` unless necessary, validate input, prefer strong typing  
- **Popular crates**:  
  - `serde` – serialization  
  - `tokio` / `async-std` – async runtime  
  - `reqwest` – HTTP client  
  - `clap` – CLI args  
  - `tracing` – structured logging  

---

## 10. Quick Reference

### Syntax
```rust
let s = String::from("hi");
for c in s.chars() { println!("{}", c); }

let v: Vec<i32> = (0..10).collect();
let squared: Vec<_> = v.iter().map(|x| x*x).collect();
```

### Useful Idioms
```rust
// Ownership transfer
fn consume(s: String) {}
let s = String::from("hi");
consume(s); // s moved

// Borrowing
fn borrow(s: &String) { println!("{}", s); }
borrow(&s);

// Option unwrap with default
let val = opt.unwrap_or(0);
```

### CLI Commands
```bash
cargo new proj
cargo run
cargo build --release
cargo test
cargo fmt
cargo clippy
```

---

✅ **With this cheatsheet, you can**:  
- Set up Rust in <30min  
- Understand ownership, errors, and traits  
- Write idiomatic, safe Rust  
- Build a working CLI app  
- Apply best practices in production

---

**Happy Rusting! 🦀**
