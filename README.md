# Unicalcorn &middot; [![GitHub license](https://img.shields.io/badge/license-MIT-blue.svg)](https://github.com/eendeego/unicalcorn/blob/master/LICENSE)

Quick glance your calendar schedule

[<img src="docs/pibow-coupe+powermate-ble-640.jpg"/>](docs/pibow-coupe+powermate-ble.jpg)

- Small footprint via [Raspberry Pi](https://www.raspberrypi.org/products/raspberry-pi-4-model-b/)
- Uses an [Unicorn Hat HD](https://shop.pimoroni.com/products/unicorn-hat-hd) to render a 4h window of calendar entries
- Syncs to a public iCal URL
- Look back 24h or forward 48h via a Griffin Powermate

## Documentation

- [Bill of Materials](/docs/bom.md)
- [Installation](/docs/installation.md)

## Bluetooth PowerMate: disable it when not in use

The `devices.powermate-ble` entry in `config.json` holds the MAC address of a
Griffin PowerMate Bluetooth. **Remove the entry (or the whole `devices`
block) unless that knob is actually paired and in range.** A value that is
not a MAC address, such as the sample's `<mac-address>`, is ignored. The USB knobs
(PowerMate USB, Rotary Trinkey) need no configuration and are not affected.

Why: while the entry is present and the knob is not connected, the app scans
for it continuously. The Bluetooth library ([noble](https://github.com/noble/noble))
records every advertising device it hears in an internal table
(`_discoveries` in `lib/hci-socket/gap.js`) and never removes an entry.
Phones, watches and earbuds rotate their Bluetooth address every few
minutes, so the table grows without bound: about 400 bytes per address,
from ~20 KB/h in a quiet house to over 300 KB/h in a busy one. On a Pi
Zero 2 W (128 MB JavaScript heap) this ended in
`FATAL ERROR: ... JavaScript heap out of memory` after 44 days of uptime.
