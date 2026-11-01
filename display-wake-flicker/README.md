# JM21 screen flicker during wake

Last updated: 2026-11-01. Device reported by the owner: **FiiO JM21**, Snapdragon 680 / Android 13 / official firmware 1.1.1.

## Symptom

The owner reports a brief full-screen vertical blue banding/ghosting flash when waking the player from screen-off. It can happen repeatedly and may clear shortly after the lock screen appears or after a screenshot is taken. The screenshot itself looked normal. The owner also observed that enabling always-on display/“Always show time and info” stopped the flicker in one trial, but the symptom later returned.

A normal screenshot only shows the image captured by Android's screenshot path. It does **not** prove that the display panel, display link, backlight, power, or timing are fault-free. Likewise, a clean software restore narrows the cause but cannot by itself prove hardware failure or rule it out.

## What has been tried

- The symptom was observed while the device was rooted and modified.
- Stock `init_boot` was restored and Magisk/root components were removed; the symptom still occurred.
- The official firmware was restored and a factory data reset was performed. The owner confirmed the flicker still occurs with a clean official system and no previously installed user apps/data. The microSD card was left untouched.
- The device had official firmware 1.1.1 on the active B slot at the last reported check. The bootloader remained unlocked. A-slot `init_boot` had been restored to stock. Re-read active slot and image state before any further partition operation.

This makes old user data, third-party apps, and Magisk unlikely to be the sole cause. It does not isolate the remaining cause to firmware or hardware.

## Sanitized observations from earlier logs

The original capture was made before the final factory reset. It is not included here because it contains device/network identifiers. Its notes reported:

- Each wake was accompanied by Qualcomm DSI `set_mode` for 750×1334 at 60 Hz and a Goodix touch controller resume/reset sequence.
- Android logged “Blocking screen on until initial contents have been drawn”; screen-on timing samples were roughly 425–782 ms.
- The capture did not show DSI timeout/FIFO/underflow/lane errors, GPU fault/hang/reset, or SurfaceFlinger/HWC crash. Absence of these log messages does not rule out a brief panel, link, backlight, power, or timing problem.
- A DSI PLL “slave PLL unavailable” warning was also present in an earlier unrooted baseline, so it was not introduced by rooting.
- SurfaceFlinger reported missed frames in a cumulative counter. Without a before/after time window, this does not prove that missed frames caused the visible flash.

## Software lead to re-check

The pre-reset `getprop` snapshot showed:

```text
debug.sf.latch_unsignaled=1
```

This is a lead, not a diagnosis. AOSP documents the historical behavior of this flag and the unsignaled-buffer latching path [here](https://source.android.com/docs/core/graphics/unsignaled-buffer-latch). The value must be read again on the clean system; the old snapshot does not establish the current value or whether it caused the symptom.

If debugging access is available, record the firmware build and active slot, read the current property, then compare repeated wake cycles with the property at its stock value and with a temporary reversible override. Change one variable at a time and reboot to restore transient debug properties. Do not change vendor display properties or flash partitions without preserving the exact current state.

## Useful next observations

Record whether the flash changes with always-on display enabled, brightness, charging, device temperature, and repeated screen-off/wake cycles. Capture a short external-camera video because Android screenshots may not capture panel-side artifacts. If the issue remains repeatable on the clean official build, send the video and sanitized timestamps to FiiO support for service assessment.

See [community cases](community-cases.md) and [technical sources](../SOURCES.md).
