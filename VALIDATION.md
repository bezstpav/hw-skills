# Validation scope

Prepared 2026-11-08. Each skill is independent; successful documentation and
metadata checks do not imply every instrument operation was physically tested.

| Skill | Evidence and limits |
|---|---|
| UT8804E | Links the maintained portable API/CLI and instrument guide. The helper's own tests and hardware scope are documented in that repository. |
| Rigol DHO800 | Model-specific command guide plus scoped DHO814 acquisition/transfer operational observations. No new capture, output stimulus or power change was made for this skill. |
| UDP3305S-E | Actual firmware 1.17 accepted VXI-11 identity/mode and protection queries, existing-threshold enable/readback. Thresholds, voltage/current setpoints and output enable states stayed unchanged. No load/trip/accuracy test. The skill uses the final channel-scoped policy; never bulk-modify unrelated channels. |
| SEGGER J-Link | Official command syntax and installed Commander 8.88 identified. No new target connection, reset, memory write or flash operation for skill preparation. |
| Nordic PPK2 | Official mode/electrical documentation and the named community API's unit/lifetime contract reviewed. No new serial ownership, source voltage, power-mode or measurement operation for preparation. |

All five frontmatter/reference structures are checked. Local hosts, device
serials, unrelated application names, private captures and credentials must stay
out of this public repository. Sources are linked, not bundled vendor manuals
or proprietary software. Optional upstream tools are not claimed to be installed.

The PSU rule is: verify and, if needed, enable **existing** OVP/OCP on the task's
channel only; never change threshold values or disable protections. Protection
requirements which prevent the operation are reported to the user. Other
channels remain untouched.

## Manufacturer-link update, 2026-11-08

Added direct user-manual links and publisher download pages, including the
UT8804E clock-setting/API limitation and the programming-download applicability
caveat. Reviewed source titles/model scope and checked skill metadata, local
links and public content. Link availability does not validate instrument
commands. No instrument connection, clock write or other hardware operation was
performed for this documentation update.
