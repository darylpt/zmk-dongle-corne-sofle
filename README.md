# đây là file tổng hợp. Hỗ trợ dongle rời dành cho máy tính không có card bluetooh

## Sofle Bluetooth RC-clock diagnostic

This branch builds only the nice!nano v2 Sofle left/right OLED firmware.
The fork's keymap and existing OLED, encoder, sleep and transmit-power settings
are unchanged. The left half retains ZMK Studio over USB.

The diagnostic disables `CONFIG_ZMK_BLE_EXPERIMENTAL_FEATURES` and
`CONFIG_ZMK_BLE_EXPERIMENTAL_SEC`, while retaining
`CONFIG_ZMK_BLE_EXPERIMENTAL_CONN=y`. In ZMK v0.3.0 this removes experimental
security/passkey behavior while keeping 2M PHY disabled. It is not a proven fix.

Compared with baseline commit `0d5a20e`, the only requested firmware setting
change is `CONFIG_CLOCK_CONTROL_NRF_K32SRC_RC=y`: use the internal low-frequency
RC clock instead of the external crystal. This diagnostic increases power draw;
it does not establish that the crystal is faulty. Keymaps and pairing settings
are unchanged. Keep the baseline firmware pair as a fallback.

GitHub Actions builds `nice_sofle_left_oled_rc_clock.uf2` and
`nice_sofle_right_oled_rc_clock.uf2`. Check the generated Kconfig output for
the RC clock selected, the crystal clock deselected and RC calibration enabled.
Experimental security/passkey entry and 2M PHY must remain disabled.
Compare resolved dependency revisions with the baseline before interpreting
typing results; configuration input loading alone is not firmware verification.

Keep the original firmware and layer screenshots. Do not use `settings_reset`
or Studio's Restore Stock Settings for this experiment. Ordinary flashing
normally preserves Studio changes; verify the layout before testing Bluetooth.
Test both halves over USB, then unplug USB and check automatic Bluetooth
reconnection and delayed, missing or repeated input across several reconnects.

If typing is unchanged or worse, restore both matching baseline UF2 images
from commit `0d5a20e` without clearing settings. Do not mix left/right images
from different diagnostic variants.
