---
marp: true
theme: espressif_training
headingDivider: 1
---

<!-- _class: lead -->
# probe-rs overview
## Scott Mabin

# Available tools
- [probe-rs-cli](https://github.com/probe-rs/probe-rs/tree/master/cli) - Standalone tool that encompasses most of probe-rs's functionality. Usable with any language. Flashing, Debug via GDB, Reset etc.
- [cargo-flash](https://github.com/probe-rs/probe-rs/tree/master/cargo-flash) & [cargo-embed](https://github.com/probe-rs/probe-rs/tree/master/cargo-embed) - Tighter integration with cargo and Rust (sort of similiar cargo-espflash)
- [probe-rs-debugger](https://github.com/probe-rs/probe-rs/tree/master/debugger) is used in conjunction with [the vscode extension](https://probe.rs/docs/tools/vscode/) to debug applications.
  - debug over DAP instead of GDB
  - a richer understanding of Rust types

# probe-rs architecture

All the tools from the previous slide share one core library, `probe-rs`. This library handles all of the flashing, debugging, and architecture-specific details which are used in the tools.

# probe-rs library
```
├── targets
│   ├── esp32c3.yaml
│   ├── esp32c6.yaml
├── src
│   ├── session.rs
│   ├── rtt.rs
│   ├── rtt/
│   ├── probe.rs
│   ├── probe/
│   ├── memory/
│   ├── lib.rs
│   ├── flashing/
│   ├── error.rs
│   ├── debug/
│   ├── core.rs
│   ├── config/
│   └── architecture/
```

# Chip target format

- probe-rs uses YAML format for describing new chips
- Provides a tool, [`target-gen`](https://github.com/probe-rs/probe-rs/tree/master/target-gen)
- The flash algorithms are extracted from an ELF file, the esp32 ones can be found [here](https://github.com/esp-rs/esp-flash-loader)

# Demo
<!-- _class: lead -->

# Tips & Tricks

- Want to try replacing openOCD in your workflow? Run `probe-rs-cli gdb --protocol jtag --chip esp32c3`. Note the default port is `1337`, instead of openOCD's `3333`

- If you have multiple probes connected you can specify via USB PID/VID (and serial number too, if you have two of the same probe) with the `--probe` argument.

# Limitations

- No Xtensa support currently - I started working on this [last summer](https://github.com/MabezDev/probe-rs/commits/xtensa) but don't have time to work on this at the moment.
- Due to the binary format required by the ROM and second-stage bootloaders of esp32's, probe-rs only knows how to flash direct boot applications

# Hive

[Hive](https://github.com/probe-rs/hive) Is an experimental shield stack for a raspberry Pi, which leverages `probe-rs` for doing HIL with up to 8 devices.
