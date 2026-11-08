---
name: segger-jlink
description: Use SEGGER J-Link Commander for probe identification, controlled SWD/JTAG attachment, reproducible memory backup and explicitly requested programming. Use when target identity, reference voltage or dump integrity matters.
---

# SEGGER J-Link

Use the installed SEGGER J-Link tools and [the command reference](references/operation.md).
Identify the probe, exact MCU/core, interface and electrical connection before
selecting a target. A family ID alone may not establish package or flash size.

- Coordinate one debug owner. Establish target power, VTref, shared GND and
  SWDIO/SWCLK or JTAG wiring. Determine whether NRST is actually connected.
- VTref is an I/O reference, not proof that the probe powers the target. Do not
  enable probe target power merely to overcome an attachment error.
- A temporary VTref override is appropriate only when actual powered target
  voltage is independently established. Record it and clear it at session end.
- Distinguish attaching, halting, resetting, RAM writes, programming, erasing,
  unlocking and option/fuse changes. Permission to identify/read is not permission
  for the others. Do not erase to bypass readout protection.
- For read-only work, prevent automatic fallback resets where supported. Target
  connection scripts/debug hardware may still affect execution; report actual
  state instead of promising an invisible attach.
- Start with a suitable conservative debug clock. On inconsistent reads, reduce
  speed and repeat independent reads; never patch a supposedly bad byte by hand.
- Confirm memory regions and lengths from the exact device documentation.
  Preserve full logs, image size, addresses and hashes. Independently repeated
  immutable-flash reads should match; running RAM is not an atomic snapshot.
- Invalidate the host flash cache between verification reads. Two cached copies
  are not two independent device reads. Blank/all-identical data alone is not
  proof that protected memory was acquired successfully.
- Program only the requested artifact/ranges after validating its identity and
  backup/verification plan. Do not silently include factory/calibration/option
  regions or reset the board as routine cleanup.

Close the session, clear temporary overrides and report the final halt/run/reset
state. Preserve the strongest verified acquisition separately from inconsistent
reads, including their original hashes and failure evidence.
