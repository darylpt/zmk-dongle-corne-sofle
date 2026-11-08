# Sofle USB-dongle configuration

The halves communicate with the dongle over BLE; the dongle sends keyboard input
to the PC over USB. This bypasses PC Bluetooth pairing, not the wireless link
between the halves and their central. It is not proprietary 2.4-GHz firmware.

## Normal firmware

Use the matching files from one successful build:

| File | Controller | Role |
| --- | --- | --- |
| `sofle_dongle.uf2` | nice!nano-compatible dongle | Central, USB keyboard, OLED and ZMK Studio |
| `sofle_left_peripheral.uf2` | Left half | BLE peripheral, OLED and encoder |
| `sofle_right_peripheral.uf2` | Right half | BLE peripheral, OLED and encoder |

The dongle stays awake on USB power. The halves retain their existing sleep
behavior; wake/reconnection still needs physical testing. This configuration
uses the baseline external crystal, not the unsuccessful RC-clock diagnostic.
Experimental BLE security/passkey behavior remains disabled, while connection
compatibility and +8 dBm transmit power are retained.

Studio is enabled only on central builds. Connect Studio to the dongle in this
mode. A left half running peripheral firmware cannot act as a standalone USB
keyboard.

## Preserved layout

`config/sofle.keymap` contains the exact live Studio capture: BAS, LOW and RAI,
each with 60 behavior/parameter bindings. This includes modifiers, keypad
usages, momentary layer keys, Bluetooth profile controls and the output toggle.
Encoder actions remain volume up/down on the left and page up/down on the right.
Legacy vendor diagrams are not the authoritative layout.

The read-only capture was backed up outside this Git repository as the
workspace sibling `dongle-mode-layout-backup.json`. It contains no device
serial number or typed input. Keep it and the original UF2 backups.

Existing Studio settings on a controller override the compiled keymap. If the
dongle shows an older layout after flashing, do not assume migration failed:
inspect which device Studio is connected to. Only after verifying the new
dongle firmware, Restore Stock Settings on that dongle loads this captured
layout; it replaces that dongle's previous Studio edits.

## First cutover attempt: normal images only

1. Close Studio. Flash each controller through its own USB bootloader drive,
   one at a time: right peripheral, dongle, then left peripheral.
2. Do not use the reset helper during this first attempt. Ordinary flashing
   does not clear stored split bonds or Studio settings.
3. Plug the dongle into the PC and power both halves. They should bond/connect
   to their central automatically. No PC Bluetooth pairing is required.
4. Verify both halves, BAS/LOW/RAI, modifiers and both encoders. Then test normal
   typing and reconnection after leaving the halves idle.

Old bonds from left-central mode can prevent this topology from pairing.
Keep the inter-half TRRS cable disconnected for flashing; do not hot-plug it.

## Optional helper and standalone fallback

| File | Purpose |
| --- | --- |
| `sofle_left_standalone_fallback.uf2` | Restores the left-central USB/Bluetooth architecture with the same captured layout |
| `settings_reset_erases_all_settings.uf2` | Destructively clears the controller's persisted settings; not normal firmware |

The fallback contains the captured layout in firmware, so it does not depend
on old Studio settings surviving a reset. The right-peripheral image above is
also used with this fallback.

### Conditional clean-bond procedure

Only perform this if normal images cannot pair and you accept clearing stored
Bluetooth profiles, split bonds, Studio edits, output preference and lighting
settings on all three controllers. No reset or flashing is performed by this
repository or its build workflow. See the
[official reset procedure](https://v0-3-branch.zmk.dev/docs/troubleshooting/connection-issues/#reset-split-keyboard-procedure).

1. Flash `settings_reset_erases_all_settings.uf2` to each controller separately:
   right half, dongle and left half. Reset all three before loading normal
   firmware. The reset image does not advertise Bluetooth or type.
2. Flash the matching normal images to right half, dongle and left half.
   Never leave the reset image on a controller.
3. Plug the dongle into the PC, power both halves and, if necessary, restart
   them once together. Do not pair the keyboard to the PC through Bluetooth.
4. Check Studio on the dongle and verify the captured layout and both halves.
   Do not restore old dongle settings over the migrated layout.

## Returning to left-central mode

Unplug the dongle, flash `sofle_left_standalone_fallback.uf2` to the left half,
and retain `sofle_right_peripheral.uf2` on the right. If old dongle bonds prevent
pairing, the same destructive reset caveat applies to the two halves before
reflashing those two files. Left-half USB typing is then available again;
cleared host Bluetooth profiles must be paired again.

Build success and compiled-layout checks are not proof of physical typing,
display, encoder or sleep/wake behavior. Compare that behavior on the keyboard
before treating this cutover as a fix.
