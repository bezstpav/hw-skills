# LAN connection and channel-scoped commands

[Manufacturer product and manuals](https://uni-trendus.com/products/udp3305s-e)
provide the model-specific command reference.
[UDP3000S user manual](https://storage.googleapis.com/uni-tdocuments/Library/UDP3000S%20Series%20DC%20Power%20Supplies%20-%20User%20Manual.pdf)
covers connections and front-panel operation. The manufacturer's
[programming-manual download](https://storage.googleapis.com/uni-tdocuments/Library/UDP3000S%20Series%20DC%20Power%20Supplies%20-%20Programming%20Manual.pdf)
and the accessible regional
[UDP3000S programming manual](https://www.uni-trend.pl/images/pliki/UDP3305SE/UDP3000S-Programming-Manual.pdf)
document VXI-11 LAN addressing. Prefer the actual model/firmware manual if it
differs. Do not assume raw TCP port 5025 solely because another supply uses it.

```sh
python -m pip install pyvisa pyvisa-py
```

```python
import os
import pyvisa

rm = pyvisa.ResourceManager("@py")
supply = rm.open_resource(f"TCPIP0::{os.environ['PSU_HOST']}::inst0::INSTR")
try:
    supply.timeout = 3000
    supply.write_termination = "\n"
    supply.read_termination = "\n"
    print(supply.query("*IDN?").strip())
    print(supply.query(":SOURce:MODE?").strip())
finally:
    supply.close()
    rm.close()
```

For NORMAL mode's selected programmable channel `n` (1, 2 or 3):

| Purpose | Query or permitted operation |
|---|---|
| OVP threshold | `:SOURce<n>:VOLTage:PROTection?` |
| OCP threshold | `:SOURce<n>:CURRent:PROTection?` |
| OVP enabled | `:SOURce<n>:VOLTage:PROTection:STATe?` |
| OCP enabled | `:SOURce<n>:CURRent:PROTection:STATe?` |
| Enable existing OVP if OFF | `:SOURce<n>:VOLTage:PROTection:STATe ON` |
| Enable existing OCP if OFF | `:SOURce<n>:CURRent:PROTection:STATe ON` |
| Output state | `:OUTPut:STATe? CH<n>` |
| Operating voltage/current | `:SOURce<n>:VOLTage?`, `:SOURce<n>:CURRent?` |
| Measured output | `:MEASure:ALL? CH<n>` |

Replace placeholders before sending commands. Query protection flags again
after enabling and compare thresholds numerically with their saved values.
Accept documented ON/OFF or 1/0 only; an unknown reply is not protected status.
Never send a numeric value to either protection threshold command.

Requested operating changes use voltage/current setters or `:APPLy` for the
explicit channel, followed by readback; channel output control uses
`:OUTPut:STATe CH<n>, ON` or `OFF` within task authorization. Do not use ALL.
The fourth auxiliary output is not proof that `SOURce4` exists. SER/PARA use
different virtual selectors; re-check their rules before any coupled operation.

The manual's query and programming sections are sufficient for basic operation;
this skill does not install a custom controller or program instrument firmware.
Firmware 1.17 has accepted the stated VXI-11 identity/mode and protection queries
and existing-threshold enable/readback workflow. That does not establish load
or protection-trip accuracy.
