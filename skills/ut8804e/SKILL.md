---
name: ut8804e
description: Read and control a UNI-T UT8804E multimeter over USB with the portable Python API and CLI. Use for live readings, measurement configuration and stored-data access.
---

# UNI-T UT8804E

Use the maintained [unit-ut8804e package](https://github.com/bezstpav/unit-ut8804e).
Read its [instrument guide](https://github.com/bezstpav/unit-ut8804e/blob/main/SKILL.md)
and [README](https://github.com/bezstpav/unit-ut8804e/blob/main/README.md) for
installation and current CLI syntax. It uses HIDAPI on Windows, macOS and Linux.

```sh
python -m pip install git+https://github.com/bezstpav/unit-ut8804e.git
ut8804e list
ut8804e --serial SERIAL_FROM_LIST read --seconds 5
```

- Keep one serialized USB owner; close another reader before acquiring the device.
  A CP2110 bridge candidate alone is not proof of the connected meter model.
- Enable the meter's communication/transmission function and use the probe
  sockets/function appropriate to the actual connected circuit. Do not infer
  resistance from a voltage-connected live circuit as a precision passive value.
- Preserve the transmitted unit, precision, validity and raw frame. Invalid,
  blank and overrange values are not zero. Values in kHz are not already Hz.
- A successful write is not a completed mode change. Treat `ER` as rejection;
  the physical selector may restrict transitions. Require fresh matching state.
- Do not retry an ambiguous HOLD toggle or storage mutation automatically: ACK
  has no transaction identifier. Use bounded waits and report timeouts.
- Save/delete/record commands require user intent and the explicit storage-write
  option. A request to read records does not authorize modifying them.
- Let the transport context manager restore the bridge UART configuration and
  enable state; do not abandon a reader process. Prior instrument streaming
  state cannot be reliably queried/restored by the protocol.

Use the package's [protocol reference](https://github.com/bezstpav/unit-ut8804e/blob/main/PROTOCOL.md)
when decoding packets. Keep hardware-tested functions distinct from synthetic
or simulated tests; do not generalize one accepted command to every mode.

## Manufacturer documentation

Use the [manufacturer product/download page](https://uni-trendus.com/products/ut8804e)
and [UT8804E user manual](https://storage.googleapis.com/uni-tdocuments/Library/UT8804E%20Digital%20Multimeters%20-%20User%20Manual_HQ.pdf)
for front-panel operation. The package's [manuals guide](https://github.com/bezstpav/unit-ut8804e/blob/main/MANUALS.md)
records clock-setting instructions and the misleading programming-download
label. The current API has no clock-set command; do not infer one from another
model's manual or claim that a requested clock change was performed.
