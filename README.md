# Hardware skills

Independent agent instructions for common laboratory instruments. Each folder
is a separate skill with its own `SKILL.md`; select only the instrument relevant
to the task. The original documentation in this repository is MIT licensed.
External applications and libraries retain their own licenses.

| Skill | Use |
|---|---|
| [ut8804e](skills/ut8804e/SKILL.md) | UNI-T multimeter measurements and control through the portable Python package |
| [rigol-dho800](skills/rigol-dho800/SKILL.md) | Rigol DHO800/DHO814 acquisition and raw waveform transfer |
| [uni-t-udp3305s-e](skills/uni-t-udp3305s-e/SKILL.md) | Channel-scoped power supply operation with fixed OVP/OCP thresholds |
| [segger-jlink](skills/segger-jlink/SKILL.md) | Probe identification, target attachment, memory backup and authorized programming |
| [nordic-ppk2](skills/nordic-ppk2/SKILL.md) | Power Profiler Kit II source/current measurement and process ownership |

## Use

Clone this repository and read the selected skill. For agents with a skills
folder, copy or link the **individual skill directory** there. Keep its
`references/` alongside `SKILL.md`. No local network addresses, device serial
numbers or bench-specific connections are included: supply those in the task
or keep them in private local configuration.

The multimeter helper is maintained separately at
[unit-ut8804e](https://github.com/bezstpav/unit-ut8804e). Other skills use existing
vendor tools or named public APIs; this repository does not bundle their code,
vendor binaries, instrument firmware or a persistent power-control service.
Do not invent a helper repository or claim that an optional integration is
installed. When a new reusable helper is needed, describe its interface and
request the intended repository before adding that dependency.

[VALIDATION.md](VALIDATION.md) distinguishes documentation checks and actual
instrument verification. A command in a manual is not proof of acceptance by
an attached instrument or firmware revision.
