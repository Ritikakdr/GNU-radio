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


Here's the GNUradio flowgraph:























start here

                  import numpy as np
                  from gnuradio import gr
                  from scipy.signal import chirp

                  class LoRaModulationBlock(gr.sync_block):
                                  """
                               This block implements LoRa modulation.
                             """

    def __init__(self, spreading_factor=7, bandwidth=125e3, coding_rate=4/5, frequency_offset=0.0):
        """
        Initialize the block with LoRa parameters.

        :param spreading_factor: Spreading factor, must be an integer between 7 and 12.
        :param bandwidth: Bandwidth in Hz, typically 125e3, 250e3, or 500e3.
        :param coding_rate: Coding rate as a fraction (e.g., 4/5).
        :param frequency_offset: Frequency offset in Hz, default is 0.
        """
        gr.sync_block.__init__(
            self,
            name='LoRa Modulation Block',  # name that will show up in GRC
            in_sig=[np.int8],  # Expecting input as bits (0s and 1s)
            out_sig=[np.complex64]  # Outputting complex modulated signal
        )
        self.spreading_factor = spreading_factor
        self.bandwidth = bandwidth
        self.coding_rate = coding_rate
        self.frequency_offset = frequency_offset

    def work(self, input_items, output_items):
        """
        Perform LoRa modulation on the input data.

        :param input_items: Input items containing bits to modulate.
        :param output_items: Output items to hold the modulated signal.
        """
        input_data = input_items[0]
        output_data = output_items[0]

        # Apply LoRa modulation logic here
        # This is a simple example using chirp modulation for demonstration purposes
        # In a real-world scenario, you would implement proper LoRa modulation logic

        # Calculate the time array for the modulation
        num_samples = len(input_data)
        time = np.arange(num_samples) / self.bandwidth

        # Apply a frequency sweep (chirp) based on the input data
        for i in range(num_samples):
            frequency = self.frequency_offset + (input_data[i] * self.bandwidth / 2**self.spreading_factor)
            output_data[i] = np.exp(1j * 2 * np.pi * frequency * time[i])

        return num_samples






![Screenshot from 2024-04-20 21-19-30](https://github.com/Ritikakdr/GNU-radio/assets/116477443/a650fc75-c95a-4d44-a94d-a16e4e64d4ac)


![image](https://github.com/Ritikakdr/GNU-radio/assets/116477443/bc4f7b94-4c9f-4a13-b104-44cf7a1cf84e)
