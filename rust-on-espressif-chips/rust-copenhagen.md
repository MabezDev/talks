---
marp: true
theme: espressif_training
headingDivider: 1
---

<!-- _class: lead -->
# Rust embedded at Espressif
## Scott Mabin

# What I'll cover today

- What is embedded?
- Why Rust for embedded?
- `async` + embedded Rust

# What is embedded?

- A system created with a specific purpose
- Usually has some real-time computing and resource constraints

<!-- SPEAKER NOTES 
  - Examples:
    - A electronic door lock
    - A temperature data logger, applications like agriculture uses
-->
# What do we mean by real-time?

- Reacting to system events with as little latency as possible

# Resource constraints

- Kilobytes of RAM, instead of Gigabytes found on modern computers
- 10's of Megahertz CPU frequencies, instead of the Gigahertz frequencies and multiple cores on modern computers
<!-- esp32 with a few hundred K of RAM, and a few MB of flash to store our program(s) -->

# Executing & Debugging programs

- Program(s) are stored in flash memory
- They need to be flashed from a host machine
- Debugging happens remotely

# Why Rust for embedded?

- Memory safety is even more important, most embedded systems do not have an MMU
- Ownership: Model physical hardware peripherals as singletons

# Why Rust for embedded

- Package management helps foster an open-source ecosystem
  - Interface trait crates like [`embedded-hal`](https://docs.rs/embedded-hal/latest/embedded_hal/)
  - Non-allocating data structure crates like [`heapless`](https://japaric.github.io/heapless/heapless/index.html)

# Why Rust for embedded - Tooling

<!-- wokwi, probe-rs, espup for Xtensa -->

# Why Rust for embedded - `async`

<!-- benefits of async for both single threaded and applications with threads -->

# `async`

<!-- TODO do I talk about how it works for embedded?? -->

# Espressif's Rust offering

<!-- chip support -->
<!-- Espressif's chip lineup, WiFi, Bluetooth, 802.15.4 etc as an example -->

<!-- Rust support, STD, no_std -->

# A real-world example

<!-- Show the esp-wifi demo? -->
<!-- What is possible today -->
