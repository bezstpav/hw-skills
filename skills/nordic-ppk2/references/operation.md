# PPK2 tools and operating contract

Preferred interactive tool: Nordic's
[Power Profiler app](https://github.com/nordicsemi/pc-nrfconnect-ppk).
Use the [official PPK2 guide](https://docs.nordicsemi.com/r/bundle/ug_ppk2/page/ug/ppk/ppk_user_guide_intro.html)
for connections and specifications.

Read the mode-specific wiring instructions before changing connections:
[Source Meter mode](https://docs.nordicsemi.com/r/bundle/ug_ppk2/page/ug/ppk/measure_current_source_meter.html)
and [Ampere Meter mode](https://docs.nordicsemi.com/r/bundle/ug_ppk2/page/ug/ppk/measure_current_ampere_meter.html).
The official user guide is the hardware authority; a community Python API is
not a replacement instrument manual.

Nordic documents DUT/external voltage from 0.8 to 5.0 V; maximum admissible
current differs by mode: source 600 mA, ampere 1 A continuous. These are hardware
limits, not arbitrary selectable current-trip settings. Verify the current
hardware guide and available USB supply before operating near a limit.
[Voltage specifications](https://docs.nordicsemi.com/r/bundle/ug_ppk2/page/ug/ppk/ppk_power_supply_specs.html)
and [current limits](https://docs.nordicsemi.com/r/bundle/ug_ppk2/page/ug/ppk/ppk_max_dut_current.html).

For automation, a community Python option is
[ppk2-api-python](https://github.com/IRNAS/ppk2-api-python). It is a separate
project with its own license; no implementation from it is included here.
Inspect the installed version before relying on lifetime or buffering behavior.

```sh
python -m pip install ppk2-api
python -c "from ppk2_api.ppk2_api import PPK2_API; print(PPK2_API.list_devices())"
```

Listing candidates is not the same as opening a powered measurement session.
Check which of the device's serial interfaces is the control interface. Serial
permissions and optional exclusive-open support differ by operating system.

## API units and sequence

With the selected version's API:

1. Open one `PPK2_API` owner with bounded serial read/write timeouts.
2. Retrieve modifiers with `get_modifiers()` before current conversion.
3. For an authorized source session, use `use_source_meter()` and validate the
   millivolt setting before `set_source_voltage(millivolts)`.
4. Use `toggle_DUT_power("ON")` / `"OFF"` only for the intended power state.
5. `start_measuring()`, `get_data()` and `get_samples(raw)` acquire/decode;
   `stop_measuring()` stops acquisition, not necessarily power.

The library's decoded current samples are in microamperes. Do not label them as
amperes or milliamperes without conversion. Use the actual sample timing and
buffering contract; sample count alone does not prove no data was dropped.
In ampere mode, follow its documented external-voltage configuration rather
than blindly applying a source-mode voltage call.

Important lifecycle detail: the reviewed `PPK2_API` finalizer sends RESET.
Its voltage conversion also clamps unsupported values. Validate before calling,
and choose cleanup deliberately. Do not patch private library methods on a live
instrument to guess at persistence; use a maintained controller with a known
shutdown contract. A new reusable service/helper should live in its own agreed
repository, not be disguised as a one-line measurement command.

No PPK2 voltage, mode, power or calibration change was required to prepare this
skill. Opening or enumerating a port is not presented as a physical profiling
acceptance test.
