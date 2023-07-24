---
marp: true
theme: espressif_training
headingDivider: 1
---

<!-- _class: lead -->
# Rust embedded at Espressif
## Scott Mabin

# What I'll cover today

- Why Rust at Espressif?
- Why Rust for embedded?
- What we offer currently
- `async` + embedded Rust

# Espressif

First some background on Espressif

# Our chips line-up

<!-- What we provide -->

# esp-idf

<!-- TODO Familiarise the audience with the C SDK -->

# Espressif & Rust

<!-- TODO how the journey started -->

# Why Rust at Espressif?

- We see it as an emerging language in the embedded (and tooling) space
- We expect to be able to write new parts of esp-idf in Rust
- We expect to rewrite certain parts of esp-idf where Rust's safety guarantees can help
- Vast & diverse open-source ecosystem
- Package management

<!-- SPEAKER NOTES
  - Mention component manager as something we've had to develop to aid users for esp-idf
 -->

# Why Rust for embedded?

- Memory safety is even more important, most embedded systems do not have an MMU
- Native `async` support, more on this later
- Separation of core library & standard library
- Package management helps foster an open source ecosystem 
  - Interface trait crates like [`embedded-hal`](https://docs.rs/embedded-hal/latest/embedded_hal/)
  - Non-allocating data structure crates like [`heapless`](https://japaric.github.io/heapless/heapless/index.html)
  - Loads more, see [awesome-embedded-rust](https://github.com/rust-embedded/awesome-embedded-rust)

<!-- SPEAKER NOTES 
  - package management to form eco system
 -->

# Tooling

<!-- wokwi, probe-rs, espup for Xtensa -->
