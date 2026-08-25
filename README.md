# cmdr-oi-bridge

A [commander](https://github.com/gbryant/commander) consumer: an **Arduino Uno R4 WiFi**
that bridges a Roomba's Open Interface to I2C. It's an I2C **slave** (`loco-bridge`
module) that a Pico master ([cmdr-robot](https://github.com/gbryant/cmdr-robot)) drives
over the wire, forwarding `CMD_LOCO_*` to the Roomba over `Serial1` and serving back a
sensor snapshot. Also provides the `oi` command, a remote console, and WiFi/telnet/OTA.

This is the **PlatformIO** flavor of a commander consumer (Arduino-framework targets use
PlatformIO + `lib_deps`; Pico/ESP32/STM32 use CMake + `FetchContent`).

## Hardware

- Arduino Uno R4 WiFi. Roomba OI on **`Serial1`** (D0/D1). **BRC wake** on **D4**
  (`brc=4` in `cmdr.toml`) so the bridge can let the base sleep/charge and wake it.
- I2C **slave** at address **66** on the Qwiic/`Wire1` port (3.3 V) — wire the Pico
  master straight in.

## Setup

Prereqs: PlatformIO and the `cmdr` tool — commander's
[getting-started guide](https://github.com/gbryant/commander/blob/main/docs/getting-started.md)
covers installing both.

```bash
cp secrets.h.example secrets.h     # then fill in your WiFi
cmdr regen                         # re-emit the dev scripts (bum/build/upload/monitor/bum-ota are gitignored)
./bum                              # build + upload + monitor
./bum-ota                          # wireless OTA (cmdr-oi-bridge.local)
```

The wrappers are gitignored rather than committed, so a fresh clone starts without
them — `cmdr regen` writes them from the current templates. If you'd rather not use
them, raw PlatformIO works just as well:

```bash
pio run -e cmdr-oi-bridge
pio run -e cmdr-oi-bridge -t upload
pio device monitor -e cmdr-oi-bridge
```

## Updating the commander framework

This project pins commander to a release tag — the `#v1.0` on the `lib_deps` git ref
in `platformio.ini`. `cmdr pull` re-fetches that same tag, so on its own it changes
nothing; to adopt a newer release, bump the `#tag` (e.g. `#v1.1`), then `cmdr pull`
and rebuild. Dropping the `#tag` floats on the default branch instead. Don't depend
on a local commander checkout as a normal workflow.
