---
name: nordic-ppk2
description: Operate Nordic Power Profiler Kit II for source-mode power and current acquisition, preserving voltage units, calibration and ownership. Use for PPK2 power profiling or an explicitly requested DUT power change.
---

# Nordic Power Profiler Kit II

Read [the operating reference](references/operation.md) and use the official
Power Profiler app or a deliberate, version-checked automation API. Select the
actual PPK2 control port by identity; do not assume a numbered serial device.

- Keep one owner of the serial interface and DUT power state. Reuse or coordinate
  an existing controller; do not kill it or open a second client as discovery.
- Identify Source Meter versus Ampere Meter wiring before changing mode.
  Source mode powers the DUT; ampere mode measures an external supply in series.
  Establish the connected voltage and permitted final power state.
- Check the model's voltage/current limits and the DUT requirements. Do not
  assume the PPK2 offers programmable laboratory-supply OVP/OCP protections.
- For source changes, validate the requested voltage before passing it to an
  API. Explicitly distinguish millivolts from volts; silent clamping is not
  successful application of the requested value.
- Load calibration/modifiers before converting current samples. Preserve units,
  invalid readings, elapsed time, raw data and buffering/loss limitations.
  Acquisition start/stop is distinct from DUT power enable/disable.
- Do not use a short-lived automation object to leave power on without examining
  its cleanup behavior: some APIs reset the device in their destructor. If power
  must persist, keep one live controller with an explicit shutdown policy or use
  the official app. Stopping a process is not proof of the final rail voltage.
- A serial write does not independently measure output voltage. When electrical
  state matters, verify it through an appropriate independent instrument.
- Do not change calibration EEPROM or device firmware during a normal profiling
  request. Closing the controller must follow the task's intended output state,
  not an assumed universal OFF or ON policy.

Do not claim a measurement is lossless or calibrated merely because an API
returned samples. Report the actual acquisition conditions and known gaps.
