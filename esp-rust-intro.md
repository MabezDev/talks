---
marp: true
theme: espressif_training
headingDivider: 1
---

<!-- _class: lead -->
# Rust Introduction
## Scott Mabin

# Background on Rust

- Rust is a systems programming language with the slogan "fast, reliable, productive: pick three."
- 1.0 release back in 2015
- 6 week release cycle

# Why Rust?

- Eliminates a whole class of memory and syncronization bugs at compile time
- Imperative language, but with elements of functional programming
- Package management with Cargo, and a rich crate ecosystem

# What is a crate?

- Synonymous with a library/project
- Two types
  - binary crate - application or project
  - library crate

# Cargo

- Manages the download and compilation of crates in a project
- Default repository is [crates.io](https://crates.io/), but allows custom repositories
- Functionality can be extended

# Typical project structure



# Hello world in Rust

```rust
//! main.rs
fn main() {
    println!("Hello world!")
}
```

# Using a library crate




# Links

- [esp-idf](https://github.com/espressif/esp-idf)
- [The esp book](https://esp-rs.github.io/book/)
- [esp-rs organisation](https://github.com/esp-rs)
- [esp-rs roadmap](https://github.com/orgs/esp-rs/projects/1)
