# cmdr-oi-bridge

A [commander](https://github.com/gbryant/commander) consumer: an **Arduino Uno R4 WiFi**
that bridges a Roomba's Open Interface to I2C. It's an I2C **slave** (`loco-bridge`
module) that a Pico master ([cmdr-robot](https://github.com/gbryant/cmdr-robot)) drives
over the wire, forwarding `CMD_LOCO_*` to the Roomba over `Serial1` and serving back a
sensor snapshot. Also provides the `oi` command, a remote console, and WiFi/telnet/OTA.

This is the **PlatformIO** flavor of a commander consumer (Arduino-framework targets use
PlatformIO + `lib_deps`; Pico/ESP32/STM32 use CMake + `FetchContent`). Naming: a `cmdr-`
prefix marks commander consumers so they don't need an umbrella folder.

## Hardware

- Arduino Uno R4 WiFi. Roomba OI on **`Serial1`** (D0/D1). **BRC wake** on **D4**
  (`brc=4` in `cmdr.toml`) so the bridge can let the base sleep/charge and wake it.
- I2C **slave** at address **66** on the Qwiic/`Wire1` port (3.3 V) — wire the Pico
  master straight in.

## Setup

```bash
cp secrets.h.example secrets.h     # then fill in your WiFi
pio run -e cmdr-oi-bridge          # build  (the bum/build/... wrappers are gitignored)
pio run -e cmdr-oi-bridge -t upload
pio device monitor -e cmdr-oi-bridge
# or, if the cmdr-generated wrappers are present locally:
./bum        # build + upload + monitor
./bum-ota    # wireless OTA (cmdr-oi-bridge.local)
```

## Updating the commander framework

Framework changes live in the commander repo; adopt the latest with **`cmdr pull`**
(rebuild after). The commander version is the `lib_deps` git ref in `platformio.ini`
(`…/commander.git` = latest default branch; append `#v1.2.0` to pin a release). Don't
depend on a local commander checkout as a normal workflow.
