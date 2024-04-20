# GNU-radio
## GNU radio blocks to Transmit(Modulate) LoRa signals.
## Introduction
LoRa Modulation is a technique based on Chirp Spread Spectrum (CSS) that modulates data using frequency shifts in a chirp signal.
So breaking down the LoRa Modulation :
The Input data coming from signal sources such as data from sensors or also receive data from a network 
### Parameter's

Bandwidth:

    The bandwidth of the chirp signal.
    Common values are 125 kHz, 250 kHz, and 500 kHz.
    Affects the data rate and range.

Spreading Factor:

    Determines the number of chips per symbol and the duration of the chirp.
    Common values range from 7 to 12.
    A higher spreading factor provides better range and interference immunity, but lower data rate.

Coding Rate:

    Specifies the rate of error correction coding.
    Common values are fractions such as 4/5, 4/6, 4/7, or 4/8.
    A higher coding rate provides better error correction but may reduce the effective data rate.

Preamble Length:

    The length of the preamble in symbols.
    The preamble helps with signal detection and synchronization.
    Typical values range from 6 to 12 symbols.

