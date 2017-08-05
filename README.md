# dome-heartbeat-monitor
Firmware for an Arduino board that will force close an Astrohaven dome if it does not receive a regular heartbeat ping from the connected control computer.

The heartbeat monitor consists of an arduino connected to a custom solid-state relay board that is connected inline with the serial connection to the dome.  The normally-closes path through the relays connects the dome to the PC as normal.  The active path switches the dome connection to a RS232-TTL converter chip that provides two-way communication between the Arduino and dome.

The PC activates the monitor by sending a byte value between `1` (0.5 seconds) and `240` (120 seconds) via USB, and can disable the monitor by sending the byte value `0`.  The firmware logic will count down from this value every half second, and a new timeout has not been recieved before it reaches 0 then the unit will close the dome.

The dome is closed by switching the serial connection from the PC to the Arduino, and then issuing a shutter step command (`A` then `B`) every 0.5 seconds until the dome returns the shutter closed status (`X`, `Y`, or `0`) or a compile-time maximum step count is reached.  Once both shutters are closed (or timed out) the dome is switched back to the control PC.

The unit reports its status back to the PC via USB every 0.5 seconds.  The status is either `0` (disabled), `254` (actively closing dome), `255` (closed dome and now inactive), or the number of half-second steps left until the timer expires. The `255` state is sticky, and must be reset by sending `0` before the heartbeat timeout can be re-enabled.

See the figures in the `docs` directory for more information on the hardware and code logic.
