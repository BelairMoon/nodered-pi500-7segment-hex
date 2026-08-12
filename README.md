# Random hex on a single 7-segment display (Node-RED / Pi 500)

A simple Node-RED flow that picks a random hex digit (`0`-`F`) every second and drives a single 1-digit 7-segment display.

## What you need

- Raspberry Pi 500 (or Pi 5) with a working 40-pin GPIO header
- 1-digit 7-segment LED display (common cathode or common anode)
- 7x 220-470 ohm resistors (one per segment; the decimal point is left off in this flow)
- Breadboard + jumper wires
- Node-RED with the `node-red-node-pi-gpio` palette nodes installed

## Pinout

The flow uses **BCM** pin numbers. Connect each segment through a resistor to the Pi GPIO, and the display's common pin to either **GND** (common cathode) or **3.3V** (common anode).

| Segment | BCM GPIO | Physical pin | Pi function |
|---------|----------|--------------|-------------|
| a       | GPIO 17  | 11           | GPCLK0      |
| b       | GPIO 27  | 13           | PWM1_2      |
| c       | GPIO 22  | 15           | GPCLK1      |
| d       | GPIO 23  | 16           | PWM0_4      |
| e       | GPIO 24  | 18           | PWM0_3      |
| f       | GPIO 25  | 22           | PCM_FS      |
| g       | GPIO 5   | 29           | GPCLK1      |
| dp      | GPIO 6   | 31           | GPCLK0      |
| **Common cathode** | - | 6, 9, 14, 20, 25, 30, 34, 39 | GND |
| **Common anode**   | - | 1, 2, 4, 17 | 3.3V (do **not** use 5V) |

### Wiring example (common cathode)

1. Plug the 7-segment display on the breadboard.
2. Identify the common pin(s) and connect to a Pi **GND** pin.
3. Connect each segment pin `a` through `g` to one end of a resistor.
4. Connect the other end of each resistor to the matching Pi GPIO pin in the table above.
5. Leave `dp` unconnected or connect it to GPIO 6 if you want the decimal point.

## Common anode or common cathode?

| Step | What to do |
|------|------------|
| 1 | Set the `commonCathode` variable at the top of the **Map to segments** function node to `true` for common-cathode, `false` for common-anode. |
| 2 | To identify by hand: use a 1 k ohm resistor and 3.3V. Touch one display pin (the common) to 3.3V and each segment pin through the resistor to GND. If a segment lights, it is **common anode**. If not, try the common pin to GND and each segment pin through the resistor to 3.3V — if it lights, it is **common cathode**. |

## How to use

1. In Node-RED, install `node-red-node-pi-gpio` from **Manage palette > Install**.
2. Open **Menu > Import > Clipboard** and paste the contents of `flows.json`.
3. Deploy. The display should show a new random hex digit every second.

## Pi 5 / Pi 500 note

The standard `node-red-node-pi-gpio` nodes use the `RPi.GPIO` Python library, which does **not** work on Raspberry Pi 5/500. For these boards you have a few options:

- **Recommended:** Install `rpi-lgpio` in the same Python environment that Node-RED uses. It is a drop-in replacement for `RPi.GPIO` and works on Pi 5/500.
- **Alternative:** Use a `libgpiod`-based Node-RED node (e.g. `node-red-contrib-libgpiod`) and replace the `rpi-gpio out` nodes with the `libgpiod` equivalent pins.

If Node-RED is running in a Docker/Podman container, you must pass the GPIO character device through, for example:

```bash
# For a Pi 5/500 the GPIO chip is usually /dev/gpiochip4
--device /dev/gpiochip4
```

## Files

- `flows.json` — the Node-RED flow to import
- `README.md` — this pinout and setup guide
