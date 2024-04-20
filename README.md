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

   
         
    import numpy as np
    from gnuradio import gr

     class blk(gr.sync_block):
        """LoRa Modulation Block"""
    
    def __init__(self, bandwidth=125e3, spreading_factor=7, coding_rate=4/5, preamble_length=8):
        """Initialize the LoRa modulation block with the specified parameters."""
        gr.sync_block.__init__(
            self,
            name='LoRa Mod',
            in_sig=[np.complex64],
            out_sig=[np.complex64]
        )
        # Store the parameters
        self.bandwidth = bandwidth
        self.spreading_factor = spreading_factor
        self.coding_rate = coding_rate
        self.preamble_length = preamble_length
    def work(self, input_items, output_items):
        """Perform LoRa modulation on the input data."""
        # Get the input and output buffers
        in_data = input_items[0]
        out_data = output_items[0]

        # Calculate the number of chips per symbol and chip duration
        chips_per_symbol = 2 ** self.spreading_factor
        chip_duration = 1 / self.bandwidth
        
        # Process each input symbol
        for i in range(len(in_data)):
            # Calculate the frequency offset for the current symbol
            frequency_offset = (in_data[i] + 0.5) * self.bandwidth / chips_per_symbol
            
            # Generate the chirp signal for the current symbol
            for j in range(chips_per_symbol):
                # Calculate the time for the current chip
                t = j * chip_duration
                
                # Calculate the chirp frequency
                chirp_frequency = frequency_offset + (j / chips_per_symbol) * self.bandwidth
                
                # Calculate the complex value for the chip (chirp signal)
                chirp_value = np.exp(2j * np.pi * chirp_frequency * t)
                
                # Apply the chirp signal to the output buffer
                out_data[i * chips_per_symbol + j] = chirp_value

        # Return the number of output items produced
        return len(out_data)
