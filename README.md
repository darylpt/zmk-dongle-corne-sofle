# đây là file tổng hợp. Hỗ trợ dongle rời dành cho máy tính không có card bluetooh

## Sofle Bluetooth diagnostic

This branch builds only the nice!nano v2 Sofle left/right OLED firmware.
The fork's keymap and existing OLED, encoder, sleep and transmit-power settings
are unchanged. The left half retains ZMK Studio over USB.

The diagnostic disables `CONFIG_ZMK_BLE_EXPERIMENTAL_FEATURES` and
`CONFIG_ZMK_BLE_EXPERIMENTAL_SEC`, while retaining
`CONFIG_ZMK_BLE_EXPERIMENTAL_CONN=y`. In ZMK v0.3.0 this removes experimental
security/passkey behavior while keeping 2M PHY disabled. It is not a proven fix.

GitHub Actions builds `nice_sofle_left_oled.uf2` and
`nice_sofle_right_oled.uf2`. Inspect the generated Kconfig output to confirm
experimental security and passkey entry are disabled and 2M PHY stays disabled.
Configuration smoke checks do not replace compilation or hardware testing.

Keep the original firmware and layer screenshots. Do not use `settings_reset`
or Studio's Restore Stock Settings for this experiment. Ordinary flashing
normally preserves Studio changes; verify the layout before testing Bluetooth.
Test both halves over USB, then unplug USB and check automatic Bluetooth
reconnection and delayed, missing or repeated input across several reconnects.
