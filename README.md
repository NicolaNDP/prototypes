# Dynamic Compressor for Max/MSP Gen~

## Overview
This Max/MSP Gen~ codebox implements a dynamic compressor that adjusts the amplitude of an input signal in real-time, according to the peak level measurement of the incoming signal. It includes the parameters for controlling threshold, ratio, hold time, release time, attack time, gain adjustment, and dry/wet mix trough the GUI interface. The compressor can also function as a peak limiter. Based on feedback design compressor where `reduction = ((sig-T)*(1/r)+T)/sig`.

## Parameters
- T (Threshold)
- r (Ratio)
- hold (Hold Time)
- release (Release Time)
- attack (Attack Time)
- gain (Gain Adjustment)
- compression (Dry/Wet Mix)

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

## DSP Code
`//params declaration//
Param T(0.5, min=0.001, max=2);	//Treshold
Param r(4, min=1, max=60);	//Ratio (if ratio==60 then it works as a peak limiter)
Param hold(4800, min=1, max=48000);	//hold time
Param release(4800, min=480, max=600000); //release time
Param attack(384, min=48, max=60000); //attack time
Param sensibility(480, min=1, max=4800); //smoothing the envelope of the signal
Param gain(1, min=0, max=4); //gain adjustment
Param compression(1, min=0, max=1); //mix dry/wet

//history declaration//
History fb(1);
History checkMin(1);

//variables declaration//
proc_reduction = 0; //variable for processed reduction
rls = release; //variable for release time

//envelope from signal level//
sig = r==60 ?
	abs(in1) : //not smoothed envelope, in case of limiter
		slide(abs(in1),sensibility,sensibility);

//reduction algorithm//
comp = fixnan((((sig - T) * 1/r) + T) / sig);
limiter = fixnan(T / sig);

reduction = r == 60 ?
	limiter :
		comp;
		
//hold count calculation//
holdCount = plusequals (1, change((sig * fb) > T) > 0);

//dynamic processing (compressing, releasing, holding)//
if ((sig * fb) > T) {
	
	rls = samplerate * 100; //blocking the release
	proc_reduction = reduction;
	
		}else{
			
			rls = release;
			proc_reduction = (holdCount >= hold) ? 
				1 :	//releasing
 					checkMin; //holding (update the minimum value)

						}

//history//
next_checkMin =  holdCount < hold ?
	min(checkMin, proc_reduction) :
		1;
checkMin = next_checkMin; //updating the minimum reduction value in hold time
//
next_fb = min(1, proc_reduction);
fb = next_fb; //feedback for processed reduction

//smoothing the reduction function//
smoothed = slide(fb, rls, attack);

//signal reduction and clamping//
procSig = r == 60 ?
	clamp((smoothed * in1 /T), -T, T): //clamp the function in case of limiter
		smoothed * in1;
		
//mixing dry/wet and gain adjustment//
outSig = mix(in1, procSig, compression) * gain;

//out//
out1 = outSig;
out2 = smoothed;`
## Screenshot 
<img width="292" alt="DynamicCompressor_Screenshot01" src="https://github.com/NicolaNDP/prototypes/assets/145101853/e87dc83e-0bde-4887-846b-e3f950ce591a">

## License
This code is provided under the [MIT License](https://opensource.org/licenses/MIT).

## Acknowledgments
- Original code by Nicola Di Paolo
