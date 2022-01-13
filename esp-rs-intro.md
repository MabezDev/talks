---
marp: true
theme: espressif_training
headingDivider: 1
---

<!-- _class: lead -->
# esp-rs Introduction

# Goals of this session

- Overview of the ecosystem
- Tooling available
- Understand how esp-idf is used in conjunction Rust
- Flash and run Standard library example on C3
- Bonus: Talk about Xtensa

# About the hardware

![esp32c3](assets/esp32-c3-devkitm-1-v1-annotated-photo.png)

# Tooling - espflash

- Rewrite of esptool.py with convenient Cargo integrations.
- Communication with the ROM bootloader via serial to flash programs.
- Handles all sorts of esp-idf specifics, partition table, bootloader etc.

# Tooling - probe-rs

- RISC-V & ARM only.
- USB-SERIAL-JTAG peripheral built into the esp32c3 silicon supported in probe-rs v0.14.
- Flashing works flawlessly, but probe-rs is not "esp-idf aware".
- Debugging RISC-V chips with probe-rs is mostly there, but unwinding the stack is buggy. See [this tracking issue](https://github.com/probe-rs/probe-rs/issues/877).

# STD - esp-idf

- A development framework for all Espressif chips since 2016 (esp32 and newer).
- Handles atomic emulation for targets (esp32c3 included) without atomics.
- esp-idf tooling is mostly written in Python, but `idf-env`, written in Rust, is rapidly gaining features.

# STD - Rust

- esp std port is `unix` like.
- Makes use of libc through the newlib component of esp-idf.
- Reference PR - [rust#87666](https://github.com/rust-lang/rust/pull/87666)
- Supports:
  - Threads
  - Networking
  - Storage (File System)
  - Rng
  - Syncronization primitives (Mutex, RWLock)

# STD - Build system

- `embuild` - manages the build and configuration of the esp-idf project.
- `-Z build-std` - Using cargo to build the standard library.
- `ldproxy` - used to to collect all the linker args from esp-idf and use them in the final link with Rust.
- **Note**: Does not play well with Windows currently, a few issues with long paths / long command line args. 

# Blinky

<!-- TODO prepare demo project with blinky for c3 devkits -->

<!-- prerequisites, ie.e tooling, compiler, compiler targets -->

# Xtensa

- Prior to the C3 all Espressif chips are Xtensa arch.
- ISA is not public, only licensees have access.
  - We made a unofficial ISA doc [available here](https://github.com/espressif/xtensa-isa-doc)
- LLVM backend developed by Espressif
- Rust for esp fork utilises the Xtensa LLVM backend

# Links

- [esp-rs organisation](https://github.com/esp-rs)
- [esp-idf](https://github.com/espressif/esp-idf)

