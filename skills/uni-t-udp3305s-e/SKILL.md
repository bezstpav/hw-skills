---
name: uni-t-udp3305s-e
description: Operate a UNI-T UDP3305S-E laboratory supply over LAN SCPI with channel-scoped OVP/OCP verification. Use for identification, voltage/current readback and explicitly requested output control.
---

# UNI-T UDP3305S-E

Use [the connection and command reference](references/operation.md). Require an
explicit host and task channel. Identify the model and NORMAL/SER/PARA mode
before choosing the physical or virtual channel; do not guess a SCPI channel
from a front-panel output count.

## Protection rule

For the **channel used by the current task only**:

1. Read existing OVP/OCP thresholds and enable flags before operation.
2. If either protection is disabled, enable it **at its existing threshold**.
3. Verify both flags are ON and numeric thresholds are unchanged before proceeding.

**Never change OVP/OCP threshold values. Never disable OVP or OCP.** Enabling
an existing disabled protection is the only permitted protection-state change.
Do not apply these writes to unrelated channels. Leave their protection flags,
limits, operating settings and outputs untouched.

If a limit is insufficient, an output trips, or protection status cannot be
confirmed, report the channel and constraint to the user. Do not raise a limit,
clear a trip and retry, load a preset, recall settings, reset the supply or
change topology as a workaround. General permission to set voltage/current does
not authorize changing protection thresholds. The operating CC current setting
is distinct from an OCP trip threshold.

## Task operation

- Establish polarity, connected load, requested voltage/current and intended
  final output state. Do not energize an unspecified channel during setup.
- Use explicit channel addressing. Read back requested operating settings and
  actual output measurements; a setpoint is not measured terminal voltage.
- Enabling a preconfigured protection can trip an already active output. Report
  any output-state change; do not automatically re-enable it or relax protection.
- Stop after an unknown response/timeout rather than repeating ambiguous writes.
  A connection test must not run `*RST`, alter LAN settings or start a list.
- Close the session and report the final channel/output/protection state. Do not
  restore an initially disabled protection at cleanup: it must remain enabled.

Do not claim that identity/readback verification tested arbitrary loads, output
accuracy, protection trip behavior or every firmware revision.
