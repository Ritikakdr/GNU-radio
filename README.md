# GNU-radio
## GNU radio blocks to recieve or transmit (modulate/Demodulate) LoRa signals.
## Introduction
Chirp spread spectrum is a type of spread spectrum modulation where the frequency of the transmitted signal varies continuously over time, LoRa uses this. Usually the spectrum bands are 868MHz,915Mhz,433Mhz.
#### To make it simpler ,let's break down how the signal flows through the GNU Radio flowgraph and what happens at each block:


1.  **Signal Source**: At the beginning of the flowgraph, the signal source block generates the baseband signal. This could be a test signal, data read from a file, or any other source you choose. The signal is typically represented as a stream of digital samples.
    
2.  **Preamble and Sync Word Insertion**: In this block, a preamble and sync word are inserted into the signal stream. The preamble is a known pattern of bits that helps the receiver synchronize with the transmitter. The sync word is a unique pattern that indicates the start of a frame. These additions help the receiver identify and synchronize with the transmitted signal.
    
3.  **Modulation**: The modulator block applies the LoRa modulation scheme to the signal. This involves encoding the digital bits into symbols suitable for transmission using LoRa modulation parameters such as bandwidth, spreading factor, and coding rate. LoRa modulation uses chirp spread spectrum modulation, where the frequency of the signal continuously varies over time.
    
4.  **Frequency Shift**: The frequency translation block shifts the baseband signal to the desired carrier frequency. This step prepares the signal for transmission at the desired RF frequency.
    
5.  **Amplitude Control**: Optionally, an amplitude modulation block can be used to control the signal's amplitude. This block may not be necessary depending on the specific requirements of your application.
    
6.  **USRP Sink**: Finally, the signal is sent to the USRP Sink block, which interfaces with the USRP hardware for transmission over the air. The USRP Sink block converts the digital signal into an analog waveform suitable for transmission and sends it to the USRP hardware for RF transmission.

   
So keeping this flow of signal in mind,I've made a GNU flowgrap for modulating LoRa signals

![image](https://github.com/Ritikakdr/GNU-radio/assets/116477443/a64bf525-35dc-4448-978e-288692457fe7)
