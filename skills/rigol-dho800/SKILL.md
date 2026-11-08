---
name: rigol-dho800
description: Configure Rigol DHO800-series oscilloscopes, especially DHO814, and acquire metadata plus raw waveform samples over SCPI. Use for trigger setup, timing measurements and numerical waveform export.
---

# Rigol DHO800 / DHO814

Start with [the connection and acquisition reference](references/operation.md).
Use the exact model's command guide; do not substitute another Rigol family.
Pass the host explicitly. DHO814 raw SCPI uses TCP port 5555; identify with
`*IDN?` before selecting a model profile.

- Establish the probe attenuation, channel, expected voltage and safe reference
  connection. An earth-referenced passive probe ground is not a floating input.
  Reuse confirmed task wiring; ask when an essential connection is unknown.
- Coordinate one controller. Record the current acquisition/channel/trigger
  state before changes and preserve unrelated settings. Do not reset as a
  connection-recovery shortcut.
- Use a meaningful trigger and enough pretrigger history. Read back applied
  settings; a successful SCPI write does not prove the scope accepted them.
- Check MAIN versus ROLL. Slow timebases can change operating behavior. A stale
  STOP indication immediately after SINGLE is not proof of a fresh capture.
- Prefer raw samples plus preamble/metadata for numerical analysis. Screenshots
  are presentation evidence, not a substitute for samples. Never fabricate an
  observed waveform from an expected pattern.
- RAW transfer requires a stopped acquisition. Validate every chunk's requested
  window, point count, payload length and errors. Reject missing/repeated chunks.
- Parse IEEE binary blocks by declared byte length, including embedded newline
  bytes. Use the preamble's conversion and the global sample index.
- Close the session and record final state. STOP stops acquisition, not power.
  If actual power-off is requested, use a verified model-specific mechanism or
  ask for physical power-off; do not claim success from an unsupported command.

The reference includes model-specific pitfalls and optional tool links. Keep
new firmware/model observations qualified rather than promoting them to a
universal SCPI contract.
