Dcc Decoder based on EthShield
==============================

Decode a DCC signal. It does not check if a signal is
a valid signal. The found packet is transmitted to
debug channel and as UDP broadcast packet.

Pin 2 of Port B (INT2) is used to catch interrupts.


# Decoding DCC signal

A DCC signal 1 typically looks like this:
````
      _________
0 ___|         |          ___
               |_________|
     '- 56 us -'- 56 us -'
````

A DCC signal 0 typically looks like this:
````
     _________________
0 __|                 |                  ___
                      |_________________|
    '---- 100 us -----'---- 100 us -----'
````

In our schematic we only look at the upper half of the
signal. So a DCC signal with 2x 1 looks like this to us:
````
      _________           _________
0 ___|         |_________|         |_____________
     '- 56 us -'- 56 us -'- 56 us -'- 56 us -'
````

If we want to know if it is a 1 or a 0 we assume the
signal is a 1 (56 us) and check between two 1 blocks:
````
      _________           _________
0 ___|         |_________|         |_____________
     ^- INT2        ^- check high/low state of pin
````

If the signal is a DCC 1, the pin should be low. If the
signal is a DCC 0, the pin should be high as the length
of a DCC 0 (100 us) has not passed yet.
