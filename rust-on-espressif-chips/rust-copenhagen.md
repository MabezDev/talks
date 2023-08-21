---
marp: true
theme: espressif_training
headingDivider: 1
---

<!-- _class: lead -->
# Rust embedded at Espressif
## Scott Mabin

# What I'll cover today

- What is an embedded system?
- Why Rust for embedded systems?
- `async` + embedded Rust
- Espressif's offerings

# What is an embedded system?

- A system created with a specific purpose
- Usually has some real-time computing and resource constraints

<!-- SPEAKER NOTES
  - hard to define precisely, the scope and resources available
  - Examples:
    - A electronic door lock
    - A temperature data logger, applications like agriculture uses
-->
# What do we mean by real-time?

- Reacting to system events with as little latency as possible
- Usually with hard deadlines for response times
- Typically measured in the order of a few milliseconds

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

<!-- SPEAKER NOTES
    - silent corruption of memory
    - crashing is the best-case scenario
 -->

# Why Rust for embedded - Tooling

- cargo, no more Makefiles!
- Package management helps foster an open-source ecosystem
  - Interface trait crates like [`embedded-hal`](https://docs.rs/embedded-hal/latest/embedded_hal/)
  - Non-allocating data structure crates like [`heapless`](https://japaric.github.io/heapless/heapless/index.html)
- probe-rs, a debugging toolkit for embedded devices

# Why Rust for embedded - `async`

- Works without alloc
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

`wake`ing a `Waker` can happen from anywhere, some examples being a call-back function from a completed operation, just another function or in many embedded cases, an interrupt handler.


# How to run futures - Executors

Where do `Poll::Pending` futures yield to? They yield back to the _executor_.

The executor is the mechanism to run futures, it handles the response to a `wake` event and then `poll`'s that future again.

A popular executor for embedded is the embassy projects executor.

# Blocking vs Async

Read the state of a button connected to a pin. Depending on whether the button is pressed, turn on or off an LED connected to another pin.

# Blocking

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

# Blocking

* Repeatedly checks for a condition to be true before proceeding
* Simple to implement, very inefficient, 100% CPU usage!

<!-- TODO add back interrupts example? -->

# Async

```rust
#[embassy_executor::main(entry = "esp_riscv_rt::entry")]
async fn main(spawner: embassy_executor::Spawner) {
    let io = IO::new(peripherals.GPIO, peripherals.IO_MUX);
    let mut output = io.pins.gpio7.into_push_pull_output();
    let input = io.pins.gpio9.into_pull_down_input();
    
    loop {
        input.wait_for_any_edge().await;
        if input.is_high() {
            output.set_high();
        } else {
            output.set_low();
        }
    }
}
```

# Async

- Structurally, it's similar to a `busy loop` but with `async`, each `.await` point allows the CPU to do something else, or even sleep to save power.
- Uses interrupts behind the scenes but the user doesn't have to worry about setting them up.

# Demo

![bg 35%](assets/wokwi-async-qr.png)

<!-- Run the two approaches on wokwi? also have the async version running on  -->
# Espressif's chip offerings

- RISC-V based ESP32-Cx, ESP32-Hx & ESP32-Px series
- Xtensa based ESP32, ESP32-Sx series
- WiFi
- Bluetooth
- IEEE 802.15.4
- Single-core and dual-core options available

<!-- Key player in the IoT market -->

# Espressif's Rust offerings

- [esp-rs/esp-hal](https://github.com/esp-rs/esp-hal) - `no_std` peripheral drivers, UART, I2C, SPI etc
- [esp-rs/esp-wifi](https://github.com/esp-rs/esp-wifi) - `no_std` WiFi and Bluetooth drivers
- [esp-rs/esp-ieee802154](https://github.com/esp-rs/esp-ieee802154) - `no_std` ieee802154 radio driver

# Bonus - STD support

- It's possible to use the Rust standard library with Espressif chips, we have a port upstream called `espidf`.
- It's based on [esp-idf](https://github.com/espressif/esp-idf), the C SDK which exposes a newlib environment which the Rust standard library can be built on top of.

<!-- speakers notes, mention lack of time to explore this topic in this talk -->

# A Rust development kit

![bg right 90%](assets/rust_board_v1_pin-layout.png)

- The [esp-rs/esp-rust-board](https://github.com/esp-rs/esp-rust-board)

# Books, resources and trainings

- Our own mdbook for getting started with Rust on Espressif chips [esp-rs/book](https://esp-rs.github.io/book/)
- We have a training pack created by Ferrous Systems available for free using this board [esp-rs/std-training](https://github.com/esp-rs/std-training).
- We also have a no_std variant using the same training materials, if the no_std option is more appealing [esp-rs/no_std-training](https://github.com/esp-rs/no_std-training)
- The [Rust embedded book](https://docs.rust-embedded.org/book/) from the Rust embedded working group
