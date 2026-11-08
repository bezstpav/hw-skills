# Connection and acquisition

Command authority: [Rigol DHO800/900 Programming Guide](https://download.rigol.com/en/Manual/Digital%20Oscilloscope/DHO800/DHO800900_ProgrammingGuide_EN.pdf).
Optional MCP integration: [rigol-mcp](https://github.com/gloveboxes/rigol-mcp).
Check whether a compatible connector is actually available before using it;
a linked repository is not an installed server.

For a simple identification query, PyVISA with a pure-Python backend works on
Windows, macOS and Linux:

```sh
python -m pip install pyvisa pyvisa-py
```

```python
import os
import pyvisa

rm = pyvisa.ResourceManager("@py")
scope = rm.open_resource(f"TCPIP0::{os.environ['SCOPE_HOST']}::5555::SOCKET")
try:
    scope.timeout = 5000  # milliseconds
    scope.write_termination = "\n"
    scope.read_termination = "\n"
    print(scope.query("*IDN?").strip())
finally:
    scope.close()
    rm.close()
```

Before acquisition, retain channel scale/offset/probe factor/coupling, timebase,
memory depth, sample rate, trigger mode/source/level/slope/sweep and run state.
Then configure only what the task needs. Inspect `:SYSTem:ERRor?`; do not erase
pre-existing diagnostics automatically with `*CLS`.

Typical workflow: configure source/channel, timebase and trigger; arm `:SINGle`;
wait for a demonstrably new record; stop for RAW memory export; select
`:WAVeform:SOURce`, `:MODE`, `:FORMat`; read `:PREamble?`; download validated
`:STARt` / `:STOP` windows with `:DATA?`. Use full keywords or documented short
forms, not invented intermediate abbreviations.

A preamble has format, type, points, count, X increment/origin/reference and
Y increment/origin/reference. For the transferred sample's **global** index:

```text
time = xorigin + (index - xreference) * xincrement
voltage = (code - yorigin - yreference) * yincrement
```

Do not apply probe scaling twice if it is already reflected in the metadata.
Parse a definite-length binary header, read exactly its payload, then handle
its terminator separately. WORD transfer has been observed as unsigned
little-endian on DHO814; confirm against the connected model/preamble.

## DHO814 operational observations

- Some timebase writes while stopped were ignored; configure while running
  when necessary, then stop and verify actual settings.
- ROLL can invalidate assumptions about offsets or single-trigger behavior.
  A timeout is not permission to repeat resets indefinitely.
- After reducing acquisition depth, old waveform windows can become invalid.
  Re-select NORM then RAW when appropriate and verify START/STOP readback.
- ASCII data may be an ordinary comma-separated line rather than a binary block.
- Preamble point counts can describe the transfer window rather than full memory.
- A displayed Math trace does not imply its numerical export is supported.

These are scoped observations, not a guarantee for every firmware. This skill
adds no signal decoder or custom capture program; use a compatible tool or
prepare a separately tested helper when a long repeated workflow needs one.
