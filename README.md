# Dynamic Compressor for Max/MSP Gen~

## Overview
This Max/MSP Gen~ codebox implements a dynamic compressor that adjusts the amplitude of an input signal in real-time, according to the peak level measurement of the incoming signal. It includes the parameters for controlling threshold, ratio, hold time, release time, attack time, gain adjustment, and dry/wet mix trough the GUI interface. The compressor can also function as a peak limiter. Based on feedback design compressor where `reduction = ((sig-T)*(1/r)+T)/sig`.

## Parameters
### T (Threshold)
### r (Ratio)
### hold (Hold Time)
### release (Release Time)
### attack (Attack Time)
### sensibility
### gain (Gain Adjustment)
### compression (Dry/Wet Mix)

## Variables
- `proc_reduction`: Variable for processed reduction
- `rls`: Variable for release time
- `holdCount`: Hold counting calculation
- `sig`: Envelope from the input signal
- `comp`, `limiter`, `reduction`: Reduction algorithms based on the signal envelope
- `smoothed`: Smoothed reduction function
- `procSig`: Processed signal after reduction and clamping
- `outSig`: Final output signal
- `out1`: Output for the processed signal
- `out2`: Output for the visualization of the reduction envelope

## Signal Flow
1. **Envelope Calculation:** Calculate the envelope of the input signal.
2. **Reduction Algorithm:** Compute the reduction factor based on `reduction = ((sig-T)*(1/r)+T)/sig`.
3. **Hold Counting:** Count the hold time when the signal exceeds the threshold.
4. **Dynamic Processing:** Adjust the release time and apply compression or hold based on the signal level.
5. **Smoothing:** Smooth the reduction function.
6. **Signal Reduction and Clamping:** Apply signal reduction and clamp for peak limiting if necessary.
7. **Dry/Wet Mixing:** Mix the input signal with the processed signal based on the compression parameter.
8. **Output:** Provide the final processed signal and the visualization of the reduction envelope.

## Usage
Download and open in Max MSP.

## Screenshot 
<img width="292" alt="DynamicCompressor_Screenshot01" src="https://github.com/NicolaNDP/prototypes/assets/145101853/e87dc83e-0bde-4887-846b-e3f950ce591a">

## License
This code is provided under the [MIT License](https://opensource.org/licenses/MIT).

## Acknowledgments
- Original code by Nicola Di Paolo
