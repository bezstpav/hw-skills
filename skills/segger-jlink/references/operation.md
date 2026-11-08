# Commander recipes

Start with SEGGER's [UM08001 J-Link / J-Trace user guide](https://kb.segger.com/UM08001)
for probe connections and software behavior. The [official downloads page](https://www.segger.com/downloads/jlink)
provides the versioned PDF manual and software package; choose the documentation
matching the installed release.

Command references: [SEGGER J-Link Commander](https://kb.segger.com/J-Link_Commander) and
[command strings](https://kb.segger.com/J-Link_Command_Strings).
Use the installed version's syntax. Linux/macOS typically provide `JLinkExe`;
Windows provides `JLink.exe`. Select a specific probe when more than one exists.

Probe-only script, without `Connect`:

```text
ShowEmuList USB
ShowFWInfo
ShowHWStatus
Exit
```

A parameterized read script can be launched with:

```text
JLinkExe -device DEVICE_NAME -if SWD -speed 100 -CommanderScript backup.jlink
```

Replace every placeholder and confirm the actual addresses/byte counts before
execution. A read-oriented `backup.jlink` may contain:

```text
Exec InhibitConnectRetries = 1
Exec ForceAttachTarget = 1
Connect
IsHalted
Exec InvalidateCache
SaveBin flash_a.bin FLASH_ADDRESS FLASH_BYTE_COUNT
Exec InvalidateCache
SaveBin flash_b.bin FLASH_ADDRESS FLASH_BYTE_COUNT
IsHalted
Exit
```

`ForceAttachTarget` skips target initialization where supported;
`InhibitConnectRetries` avoids fallback attempts which can toggle reset. Neither
is a guarantee that every MCU/debug configuration is unaffected. Do not add
halt/reset/run commands when only a running read was requested. Validate file
size and hashes with platform tools or Python's `hashlib`, and keep the logs.

## Voltage reference

If a correctly powered, independently confirmed 3.3 V target cannot route VTref,
a supported temporary override is:

```text
Exec SetVTrefTmp 3300
```

Use the **actual** voltage in millivolts, not an assumed default. Clear with
`Exec SetVTrefTmp 0`; closing the session also withdraws it. A later status value
may reflect the override rather than a fresh physical measurement, so verify
normal sensing in a new session. This does not energize the target.

`Power on` controls target power on supported probes and is a separate operation;
it can involve a 5 V supply. Do not equate it with a 3.3 V reference selection.

## Authorized writes

`LoadFile` programs supported artifacts and normally resets/verifies according
to its options. `VerifyBin` reads back for comparison; `SaveBin` acquires a region.
Use the manual for explicit addresses and reset behavior. RAM loading also
changes the device and execution may need explicit SP/PC setup; it is not an
implicit read-only operation. Never use an erase/unlock/fuse change to improve
an otherwise unauthorized backup attempt.
