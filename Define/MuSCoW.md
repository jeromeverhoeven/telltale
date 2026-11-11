# MuSCoW: Telltale

|Instructions|
|----|
|A MuSCoW mean **Mu**st, **S**hould, **Co**uld, **W**ont: each line should be a user story, constraint, criteria, feature, parameter, or something else that describes and defines you project. If it is important to the project write it down, if you don't how is the next guy supposed to know to consider it.|
|Each line must explained and then dispositioned as Must/Should/Could/Wont.|

**Telltale** is a tool to test and monitor embedded electronics (PCBAs and control systems). It has two parts: a **monitor** that reads the RS-232 stream from Wellington style products, shows every board variable live and records sessions to a file, and a **dehu IO emulator** that stands in for every input and output a dehumidifier control board connects to, so a board can be exercised on the bench without a real dehu.

## Monitor: Core Requirements
| Num | Item | Expansion | MuSCoW | notes |
|----|----|----|----|----|
|1|Read all board variables | Get every board variable out of the RS-232 stream of Wellington style products | **Must** | Need the stream format: baud/framing, message structure, variable names |
|2|Readable display | Show all variables in an easy to read format | **Must** | |
|3|Recording session | The user can start (and stop) a recording session that stores all variables to a .csv or .xlsx file | **Must** | |
|4|Arrange variables | The user can arrange the variables to see the ones they are interested in | *Should* | e.g. reorder, hide, pin to top |
|5|Near real time | Display lag under 1 second from the board sending a value to it showing on screen | *Should* | Measurable for testing in Destroy |
|6|Intuitive UI | Someone new to Telltale can connect, read and record without instructions | *Should* | |
|7|Cost | Should cost less than $500 | *Should* | |
|8|Extendable | Can be extended for more advanced monitoring and testing | Could | Keep parsing, display and recording as separate parts so new features plug in |
|9|Remote monitoring | The interface can be monitored remotely over the internet | Could | Affects architecture choice in Design |

## Monitor: Proposed (review)
These came out of earlier discussion. Keep, change or delete each one.

| Num | Item | Expansion | MuSCoW | notes |
|----|----|----|----|----|
|20|Port settings | Selectable baud rate, data bits, parity, stop bits | *Should* | Could be fixed if all Wellington products are the same |
|21|Disconnect/reconnect | Detect a board unplug or reset and reconnect without losing the recording | *Should* | |
|22|Bad data handling | Lines that can't be parsed are shown/logged as raw text instead of dropped | *Should* | |
|23|Timestamps | Every recorded row gets a host timestamp | **Must** | Needed to make a recording usable |
|24|Long recordings | Multi-hour/overnight recording without slowing down or losing data | *Should* | |
|25|Highlight | Highlight errors, warnings and out-of-range values | Could | |
|26|Charts | Plot numeric variables over time | Could | |
|27|Board presets | Saved settings for each product type | Could | |
|28|Send commands | Send commands back to the board | Could | Needed for future test sequences |

## Dehu IO Emulator
Emulates all the IO of a dehu control board. "Board" below means the dehu control board under test.

| Num | Item | Expansion | MuSCoW | notes |
|----|----|----|----|----|
|100|Emulate all board IO | The emulator connects to every input and output of the dehu control board and stands in for the real dehu components | **Must** | |
|101|BACnet | Emulated values are set and read over BACnet | **Must** | From Nov 10 discussion. BACnet/IP or MS/TP TBD |
|110|Thermistor | Presents a resistance to the board's thermistor input that matches a set temperature | **Must** | Need thermistor part/curve (e.g. 10k NTC, Beta) and how many inputs |
|111|I2C temperature/humidity sensor | Acts as the I2C temperature/humidity sensor: answers the board at the sensor's address with set temperature and RH values | **Must** | Need sensor part number and I2C address |
|112|Refrigerant pressure switch | Presents the pressure switch to the board as open or closed | **Must** | Need contact type (NO/NC) and high/low side count |
|113|A2L gas sensor | Emulates the A2L refrigerant leak sensor: normal, leak detected, and sensor fault | **Must** | Need sensor part and interface (analog, digital, UART...) |
|114|Phase detector | Emulates the phase detector: phase OK and phase fault | **Must** | Need detector output type |
|115|Fan | Emulates the fan: power input, speed input, tachometer output, 10V, and ground reference. Detects when the board powers the fan, measures the speed command, outputs a tach signal that follows the commanded speed, and provides the 10V reference | **Must** | Need speed command type (0-10V/PWM), tach pulses per rev, max RPM, fan count |
|116|Compressor | Emulates the compressor as a power input: detects when the board energizes the compressor | **Must** | Need compressor output voltage (24VAC? line?) |
|117|Float switch | Presents the float to the board's float input as open or closed | **Must** | |
|118|Pump | Detects when the board energizes the pump output | **Must** | Need pump output voltage |

### Dehu IO Emulator: Proposed (review)
| Num | Item | Expansion | MuSCoW | notes |
|----|----|----|----|----|
|130|Safe power sensing | Power inputs (compressor, fan, pump) are isolated and rated for the board's output voltage | *Should* | Mandatory if any output is line voltage |
|131|Fault injection | Force faults: open/shorted thermistor, I2C sensor not responding, fan tach lost | *Should* | Tests the board's fault handling |
|132|Dehu behavior model | Values react like a real dehu, e.g. RH falls while the compressor runs, tach follows speed command | Could | |
|133|Monitor integration | Emulator points appear in the Telltale monitor and recordings alongside the board's RS-232 variables | Could | One recording shows stimulus and board response |
|134|Scripted scenarios | Play a timed sequence of emulated values (e.g. high RH, then float trip) | Could | Toward automated testing (80) |

## Not Now
| Num | Item | Expansion | MuSCoW | notes |
|----|----|----|----|----|
|80|Automated test sequences | Scripted pass/fail tests of a board | `Wont` | Future phase, enabled by 8 |
|81|User accounts | Logins and per-user settings | `Wont` | Not needed for bench use |
