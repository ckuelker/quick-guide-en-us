---
title: Rust Hello World
author: Christian Külker
date: 2023-01-18
version: 0.1.3
type: doc
disclaimer: True
toc: True
categories:
- Rust
tags:
- Hello-World
commands:
- aptitude
- rustc
description: The most simple straight forward 'hello world!' program for Rust

---

The Rust programming language is syntactically similar to C++ and is designed
for performance and safety, especially safe concurrency.

## Installation

The usual assumption is that you need to install `rust` in order to use or
compile `rust`. However, you must install **`rustc`** in order to compile
programs written in Rust.

```bash
aptitude install rustc
```

For Debian 10 Buster the dependencies are:

```
cargo gdb libbabeltrace-ctf1 libbabeltrace1 libc6-dbg libdw1 libhttp-parser2.1
libllvm7 libstd-rust-1.34 libstd-rust-dev rust-gdb
```

For Debian 11 Bullseye the dependencies are:

```
cargo gdb libbabeltrace1 libc6-dbg libdebuginfod1 libsource-highlight-common
libsource-highlight4v5 libstd-rust-1.48 libstd-rust-dev rust-gdb
```

## Hello World

The famous source code for 'Hello World!' in Rust is:

```bash
echo 'fn main() {' > hello-world.rs
echo '    println!("Hello World!");' >> hello-world.rs
echo '}' >> hello-world.rs
```

This will give the file [`hello-world.rs`](hello-world.rs) with the content

```rust
fn main() {
    println!("Hello World!");
}
```

Compiling

```bash
rustc hello-world.rs
```

Executing

```bash
./hello-world
Hello World!
```

## Documentation

- [Website](https://www.rust-lang.org/)
- [Official book](https://doc.rust-lang.org/book/)
- [Official syntax reference](https://doc.rust-lang.org/reference/)
- [Repository web page]( https://github.com/rust-lang/rust)
- [Repository](https://github.com/rust-lang/rust.git)

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.3   | 2023-01-18 | Improve writing, add hello-world.rs link             |
| 0.1.2   | 2022-10-26 | Documentation                                        |
| 0.1.1   | 2022-07-07 | Debian 11 Bullseye, shell->bash, commands            |
| 0.1.0   | 2020-09-05 | Initial release                                      |

