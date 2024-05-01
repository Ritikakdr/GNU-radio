# GNU-radio
## GNU radio blocks to Transmit(Modulate) LoRa signals.
## Introduction
LoRa Modulation is a technique based on Chirp Spread Spectrum (CSS) that modulates data using frequency shifts in a chirp signal.
So breaking down the LoRa Modulation :
The Input data coming from signal sources such as data from sensors or also receive data from a network 
### Parameter's

Bandwidth:

The bandwidth of the chirp signal.
Common values are 125 kHz, 250 kHz, and 500 kHz.Affects the data rate and range.

Spreading Factor:

Determines the number of chips per symbol and the duration of the chirp.Common values range from 7 to 12.
A higher spreading factor provides better range and interference immunity, but lower data rate.

Coding Rate:

Specifies the rate of error correction coding.Common values are fractions such as 4/5, 4/6, 4/7, or 4/8.
A higher coding rate provides better error correction but may reduce the effective data rate.

Preamble Length:

The length of the preamble in symbols.
The preamble helps with signal detection and synchronization.Typical values range from 6 to 12 symbols.

   
## CODE
 The code given below is for a GNU radio python block for LoRa modulation. It basically accepts a complex input symbols and modulates them using LoRa's Chirp Spread Spectrum (CSS) technique.
 The code mainly uses the 4 parameter's that is Bandwidth, spreading factor, coding rate and the preamble length to control the modulation process.
 so here's how it works , for each input signal it calculates the frequency offset and generates a chirp signal based on the parameters. The chirp signal that has been modulated is then written to the output buffer for transmission.

 ### for loop
 Used a for loop to process each input symbol,here's how it works
  So for every given input symbol it calculates the frequency offset based on the value,bandwidth and chirps per symbol. frequency offset determines the center frequency for the chirp signal.,it basically maps each input symbol to a specific frequency range. Then another for loop calculates the chirp signal generation, we can calculate the chips per symbol and chip duration with the help of the parameters. And then the chirp frequency is calculated, once we know the chirp frequency it is simple to calculate the chirp value . A series of these chirsp values generate a modulation. 


    
            import numpy as np
    from gnuradio import gr

    class LoRaModulationBlock(gr.sync_block):
    def __init__(self, bandwidth=125e3, spreading_factor=7, sample_rate=500e3, center_freq=868e6):
        # Initialize the base class
        super().__init__(
            name="LoRa Modulation Block",
            in_sig=[np.int8],
            out_sig=[np.complex64]
        )
        
        # Set LoRa parameters
        self.bandwidth = bandwidth
        self.spreading_factor = spreading_factor
        self.sample_rate = sample_rate
        self.center_freq = center_freq
        
        # Calculate the number of samples per symbol
        self.samples_per_symbol = 2 ** spreading_factor
        
        # Calculate the chirp rate
        self.chirp_rate = self.bandwidth *(self.bandwidth / self.samples_per_symbol)
        
        # Calculate the start frequency for up-chirp and down-chirp
        self.start_freq = center_freq - (bandwidth / 2)
        
        # Initialize time index
        self.time_index = 0
        
    def work(self, input_items, output_items):
        # Get the output array
        out = output_items[0]
        
        # Process each input sample
        for i in range(len(out)):
            # Calculate the time variable t
            t = i / self.sample_rate
            
            # Calculate the phase for up-chirp and down-chirp
            phase_up = 2 * np.pi * (self.start_freq * t + (self.chirp_rate * t ** 2) / 2)
            phase_down = 2 * np.pi * (self.start_freq * t - (self.chirp_rate * t ** 2) / 2)
            
            # Generate the complex up-chirp and down-chirp signals
            up_chirp = np.exp(1j * phase_up)
            down_chirp = np.exp(1j * phase_down)
            
            # Alternate between up-chirp and down-chirp based on input data
            if input_items[0][i] == 0:
                out[i] = up_chirp
            else:
                out[i] = down_chirp
            
            # Increment time index for the next iteration
            self.time_index += 1
        
        # Return the number of processed samples
        return len(out)



Here's the GNUradio flowgraph:
![Screenshot from 2024-04-20 21-19-30](https://github.com/Ritikakdr/GNU-radio/assets/116477443/a650fc75-c95a-4d44-a94d-a16e4e64d4ac)



Here's the waterfall:
![image](https://github.com/Ritikakdr/GNU-radio/assets/116477443/f57ceb70-a039-4316-8634-7ddfcb2e463a)


![image](https://github.com/Ritikakdr/GNU-radio/assets/116477443/bc4f7b94-4c9f-4a13-b104-44cf7a1cf84e)
