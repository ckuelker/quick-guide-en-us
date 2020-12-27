---
title: Rust Hello World
author: Christian Külker
date: 2020-09-05
version: 0.1.0
type: doc
disclaimer: True
TOC: True
categories:
- Rust
tags:
- Hello-World
description: The most simple straight forward 'hello world!' program for Rust

---

The programming language Rust is syntactically similar to C++ and designed for
performance and safety, especially safe concurrency.

## Installation

The usual assumption is to install `rust` to use `rust`. However you have to
install **`rustc`** to compile programs written in Rust.

```shell
aptitude install rustc
```

For Debian 10 Buster the dependencies are:

```
cargo gdb libbabeltrace-ctf1 libbabeltrace1 libc6-dbg libdw1 libhttp-parser2.1
libllvm7 libstd-rust-1.34 libstd-rust-dev rust-gdb
```

## Hello World

The famous source code for 'Hello World!' in Rust is:

```shell
echo 'fn main() {' > hello-world.rs
echo '    println!("Hello World!");' >> hello-world.rs
echo '}' >> hello-world.rs
```

This will give the file `hello-world.rs` with the content

```rust
fn main() {
    println!("Hello World!");
}
```

Compiling

```shell
rustc hello-world.rs
```

Executing

```shell
./hello-world
Hello World!
```

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.0   | 2020-09-05 | Initial release                                      |



