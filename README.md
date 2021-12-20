RF433any
========

Uses a RF433Mhz component plugged on an Arduino to listen to signals and decode
it.


Installation
------------

Download a zip of this repository, then include it from the Arduino IDE.


Schematic
---------

1. Arduino board. Tested with NANO and UNO.

2. Radio Frequence 433Mhz RECEIVER like MX-RM-5V.

RF433 RECEIVER data pin must be plugged on a board' digital PIN that can
trigger interrupts, that is, D2 or D3.

This RECEIVER PIN is defined at the time a 'Track' object is created. This
library does not set it at compile time.

See file schema.fzz (Fritzing format) or schema.png, for a circuit example with
receiver plugged on D2.


Usage
-----

See [examples/01_output-received-code/01_output-received-code.ino](examples/01_output-received-code/01_output-received-code.ino)
for an example.

See [examples/02_output-signal-timings/02_output-signal-timings.ino](examples/02_output-signal-timings/02_output-signal-timings.ino)
for another example.

See [examples/03_react_on_code/03_react_on_code.ino](examples/03_react_on_code/03_react_on_code.ino)
for an example with code check.

See [examples/04_callback/04_callback.ino](examples/04_callback/04_callback.ino)
for an example with callback functions registered to be called when specific
codes are received.

Use [examples/05_print_code_for_RF433recv_lib/05_print_code_for_RF433recv_lib.ino](examples/05_print_code_for_RF433recv_lib/05_print_code_for_RF433recv_lib.ino)
to display code characteristics in a way that is usable by the library RF433recv.

More details
------------

The library assumes one of the following auto-synchronization protocols (tiret
for high signal, underscore for low radio signal):

    Tri-bit            __-   versus   _--

    Tri-bit inverted   -__   versus   --_

    Manchester         _-    versus   -_

The decoder tries to be as flexible as possible to decode any protocols,
without pre-knowledge about signal timings. To be generic enough, only the
_relationships_ between timings are analyzed, to deduct the 'short' and 'long'
duration on 'low' and 'high' radio frequence signal value. No pre-defined
timing is used.

Most often, 'long' is twice as long as 'short', and the durations on the low
signal are the same as on the high signal, but this library doesn't assume it.
'long' are not necessarily twice as long as 'short', and the high signal
timings can be totally different from the low signal timings.

The signal can also contain the below:

* A 'prefix' made of a first succession of 'low, high' durations that don't
match short and long durations encountered later. Such a prefix has been seen
on FLO/R telecommands, likely, to distinguish between FLO (fixed code) and
FLO/R (rolling code).

* A 'sync' prefix made of a succession of low and high of the same duration.
Note such a prefix could be regarded as Manchester encoding of as many '0' bits
(when using RF433ANY_CONV0, see below). The library assumes that such a
sequence, if seen at the beginning ('short' and 'long' durations are not yet
known), corresponds to a synchronization prefix, not to a Manchester encoding
of '0' bits.

The signal decoding can be done using two conventions.
Switching from one convention to another for the same signal will simply invert
bit values.


Bit value depending on convention
---------------------------------

|                  | Signal shape          | RF433ANY_CONV0 | RF433ANY_CONV1 |
| ---------------- | --------------------- | -------------- | -------------- |
| Tri-bit          | low short, high long  |        0       |        1       |
| Tri-bit          | low long, high short  |        1       |        0       |
| Tri-bit Inverted | high short, low long  |        0       |        1       |
| Tri-bit Inverted | high long, low short  |        1       |        0       |
| Manchester       | low short, high short |        0       |        1       |
| Manchester       | high short, low short |        1       |        0       |


About RF433any versus RF433recv
-------------------------------

RF433recv is found here:
[https://github.com/sebmillet/RF433recv](https://github.com/sebmillet/RF433recv)

- RF433any has no pre-defined idea of the code to analyze (nature of encoding,
code timings, number of bits).

- RF433recv works the other way round: it works with the exact code
characteristics.

**Then what is RF433recv good for?**

Actually RF433any, while being 'easy and universal', consumes *a lot* of
memory, and this can be problematic.

**How to get the best of the two worlds**

You can use RF433any to get the exact code characteristics and re-use it with
RF433recv library.

This is the goal of
[examples/05_print_code_for_RF433recv_lib/05_print_code_for_RF433recv_lib.ino](examples/05_print_code_for_RF433recv_lib/05_print_code_for_RF433recv_lib.ino)

You can copy-paste the output of this sketch to call RF433recv library.

