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
<!-- TODO expand, add context from Linux to an embedded system -->

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

# Why Rust for embedded - Tooling

- cargo, no more Makefiles!
- Package management helps foster an open-source ecosystem
  - Interface trait crates like [`embedded-hal`](https://docs.rs/embedded-hal/latest/embedded_hal/)
  - Non-allocating data structure crates like [`heapless`](https://japaric.github.io/heapless/heapless/index.html)
- probe-rs, a debugging toolkit for embedded devices

# Why Rust for embedded - `async`

- Provides single-threaded concurrency (multitasking)
- Can run on a single stack, great for resource-constrained microcontrollers
- Write asynchronous code that has similar ergonomics and readability as synchronous code

<!-- SPEAKER NOTES
  Compare the use of async in a server to get more out of system threads to a single thread case in embedded
 -->

# How does `async` work?

You can only `await` something that implements the `Future` trait.

The `Future` trait has one required method, `poll` which returns either `Poll::Ready(_)` if the asynchronous operation is complete, or `Poll::Pending` if it needs to be polled again later.

# `async`'s relationship with `Future`

- `async` functions are compiled down to state machines that implement the  `Future` trait. This is handled completely by the Rust compiler
- Therefore `Future` is the building block for any asynchronous operation in Rust.

# When to poll?

You _could_ just `poll` the future in a hot loop, but this is not very efficient and will block other `async` operations from running.

```rust
while let Poll::Pending = some_fut.poll() {
    // 100% CPU used here waiting for `Poll::Ready(_)`
}
```

We'd like to do other things until the `async` operation is ready. This is where the `Waker` concept is introduced.

# The `Waker`

A `Waker` is something that can be used to signal that a future should be polled again.

`wake`ing a `Waker` can happen from anywhere, some examples being an interrupt handler, a call back function or just another function.

# Blocking vs Async

Read the state of a button connected to a pin. Depending on whether the button is pressed, turn on or off an LED connected to another pin.

# Blocking

* Repeatedly checks for a condition to be true before proceeding
* Simple to implement, very inefficient

```rust
let io = IO::new(peripherals.GPIO, peripherals.IO_MUX);
let mut led = io.pins.gpio7.into_push_pull_output();
let button = io.pins.gpio9.into_pull_down_input();

loop {
    if button.is_high().unwrap() {
        led.set_high().unwrap();
    } else {
        led.set_low().unwrap();
    }
}
```

# Async

```rust
#[embassy_executor::main(entry = "esp_riscv_rt::entry")]
async fn main(spawner: embassy_executor::Spawner) {
    let io = IO::new(peripherals.GPIO, peripherals.IO_MUX);
    let mut output = io.pins.gpio7.into_push_pull_output();
    let input = io.pins.gpio9.into_pull_down_input();
    
    loop {
        match select(
            input.wait_for_rising_edge(),
            input.wait_for_falling_edge(),
        ).await { // function "pauses" here at `await`
            Either::First(_) => output.set_high(),
            Either::Second(_) => output.set_low(),
        }
    }
}
```

# Async

- Structurally, it's similar to a `busy loop` but with `async`, each `.await` point allows the CPU to do something else, or even sleep to save power.
- Uses interrupts behind the scenes but the user doesn't have to worry about setting them up.

# Espressif's chip offerings

# Espressif's Rust offerings

<!-- chip support -->
<!-- Espressif's chip lineup, WiFi, Bluetooth, 802.15.4 etc as an example -->

<!-- Rust support, STD, no_std -->

# A real-world example

<!-- Show the esp-wifi demo? -->
<!-- What is possible today -->
