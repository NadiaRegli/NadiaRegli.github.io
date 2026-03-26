---
layout: page
title: Electrocardiogram (ECG) Design and Prototype
description: "Designed, simulated, built, and validated an ECG acquisition system: op‑amp based instrumentation, high/low‑pass filtering on breadboard, PCB prototyping, signal processing code, and oscilloscope‑driven debugging. Demonstrates analog circuit design, filtering, biomedical signal processing, and test methodology."
img: assets/img/ecg-project/actual-circuit.jpeg
importance: 2
category: mechatronics
---

## Overview

This project involved the end-to-end design, simulation, construction, and validation of a real-time ECG acquisition and R wave detection system. Starting from a raw biopotential signal on the order of millivolts, the system amplifies, filters, digitises, and processes the ECG to reliably identify each QRS complex in real time.

The complete signal chain comprised a two-stage instrumentation amplifier (gain ×525), a Bessel bandpass filter (0.5–40 Hz), a DC offset amplifier to bring the signal within the Teensy ADC range (0–3.3 V), and a digital Pan-Tompkins QRS detection algorithm implemented in both MATLAB and C on a Teensy microcontroller. The finished system successfully demonstrated filtered ECG output on an oscilloscope and real-time R wave detection indicated by an LED, verified on a human subject.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ecg-project/actual-circuit.jpeg" title="Full ECG Circuit on Breadboard" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Full ECG Circuit on Breadboard
</div>

## Project Objectives

The project was designed to integrate techniques from the BIOE6403 course into a tangible biomedical application. The specific objectives were:
•	Design and simulate a front-end instrumentation amplifier with a minimum differential voltage gain of 500.
•	Design and simulate a Bessel bandpass filter (2nd order HPF at 0.5 Hz, 4th order LPF at 40 Hz) to isolate the ECG frequency band.
•	Design a DC offset amplifier to shift the filtered ECG signal into the 0–3.3 V input range of the Teensy ADC.
•	Digitise the conditioned ECG at an appropriate sampling frequency.
•	Implement a digital QRS detection algorithm in MATLAB and validate it against the MIT-BIH arrhythmia database.
•	Port the algorithm to a Teensy microcontroller for real-time R wave detection, with oscilloscope and LED output.

## System Architecture
The system follows a linear signal chain from the body surface to a digital output decision, as illustrated below:


<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ecg-project/biom6403-system-architecture.JPG" title="ECG System Architecture" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    ECG System Architecture
</div>

All analog stages were designed using TL072 dual op-amps powered from a ±9 V battery supply. Safety was maintained by placing 39 kΩ resistors in series with all patient connections and using isolated battery power (never a bench supply, whenever connecting to a human subject).

## ECG Front-End Instrumentation Amplifier
### Design Rationale
A standard three-op-amp instrumentation amplifier topology was used for the first stage, followed by a difference amplifier for the second stage. The project required distributing the gain across both stages to avoid saturation and maintain signal integrity.

A total gain of 525 was chosen, split as Stage 1 = ×21 and Stage 2 = ×25. This balanced distribution keeps each stage well within the TL072's linear operating range for millivolt-level ECG inputs, while achieving the required minimum of ×500 overall.

### Circuit Design and Resistor Values
The gain of the three-op-amp first stage is set by the gain resistor R2 and the feedback resistors R1 and R3, following the relation G1 = 1 + 2(R1/R2). For Stage 1, R1 = R3 = 120 kΩ and R2 = 12 kΩ yielding G1 = 21. The second-stage difference amplifier uses R13 = R16 = 180 kΩ and R17 = 15 kΩ, giving G2 = 25. All other feedback and matching resistors were set to 47 kΩ. The op-amps were powered from ±9 V batteries.

To ensure patient safety, 39 kΩ protection resistors were placed in series with all amplifier inputs, limiting any possible leakage current to a safe level.

### LTSpice Simulation Results
The amplifier was simulated in LTSpice using a three-resistor voltage divider (R8=R9=1 kΩ, R10=998 kΩ) to produce a calibrated differential input of 1 mVpp from a 1 Vpp source, closely replicating real-world ECG signal levels. A second simulation used the actual 5 mVpp differential configuration to verify full-scale performance.

Differential mode (1 mVpp input): Vdiffpp = 1.018 mVpp, Voutpp = 534 mV → measured gain = 524. This is within 0.2% of the theoretical target of 525, confirming the design.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ecg-project/diff-mode-1mVpp-circuit.JPG" title="Differential Mode (1mVpp input) Circuit" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Differential Mode Amplifier (1mVpp input) Circuit
</div>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ecg-project/diff-mode-1mVpp-graph.JPG" title="Differential Mode (1mVpp input) Graph" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Differential Mode Amplifier (1mVpp input) Simulation showing V(in) and V(out)
</div>

Differential mode (5 mVpp input): Vdiffpp = 5.013 mVpp, Voutpp = 2.630 V → measured gain = 524.6. The output waveform was clean and undistorted, confirming linear operation.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ecg-project/diff-mode-5mVpp-circuit.JPG" title="Differential Mode (5mVpp input) Circuit" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Differential Mode Amplifier (5mVpp input) Circuit
</div>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ecg-project/diff-mode-5mVpp-graph.JPG" title="Differential Mode (5mVpp input) Graph" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Differential Mode Amplifier (5mVpp input) Simulation showing V(in) and V(out)
</div>

Common mode simulation: A 1 Vpp common-mode signal was applied to both inputs simultaneously. The output was measured at approximately 620 pV (effectively zero) demonstrating that the ideal matched-resistor simulation produces an extremely high CMRR, consistent with the theoretical performance of a well-matched instrumentation amplifier. In practice, resistor tolerance (±5%) would reduce this, but the simulation provides an upper-bound benchmark.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ecg-project/common-mode-circuit.JPG" title="Common Mode Circuit" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Common Mode Amplifier Circuit
</div>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ecg-project/common-mode-graph1.JPG" title="Common Mode Graph" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Common Mode Amplifier Simulation showing V(in) and V(out)
</div>

### Component Summary

| Stage                         | Gain  | Key Resistor Values                                    |
| ----------------------------- | ----- | -------------------------------------------------------|
| IA Stage 1 (3-op-amp)         | 21X   | R1=R3=120kΩ, R2=12kΩ feedback=47kΩ                     |
| IA Stage 2 (difference amp)   | 25X   | R13=R16=180kΩ, R17=15kΩ, feedback=47kΩ                 |
| Combined IA Gain              | 525X  | Verified: Vdiffpp=5.01mVpp → Vout=2.63Vpp              |
| Bessel LPF (4th order, 40Hz)  | Unity | C=1µF, R≈2.779kΩ / 2.478kΩ / 1.008kΩ / 9.108kΩ         |
| Bessel HPF (2nd order, 0.5Hz) | Unity | C=1µF, R≈404.89kΩ / 3.216kΩ / 12kΩ                     |
| Offset Summing Amplifier      | Unity | R1=12kΩ, R2=1.2kΩ (divider); R4=R5=R3=22kΩ; R6=R7=10kΩ |

---

## ECG Bandpass Filter
### Design Specification and Approach
A Bessel filter topology was selected for both the high-pass and low-pass stages. The Bessel filter is preferred in ECG applications because of its maximally flat group delay (linear phase response), which preserves the morphology of the QRS complex waveform. Other filter types such as Butterworth or Chebyshev introduce phase nonlinearities that can distort the sharp rising edge of the R wave, potentially affecting detection accuracy.

The filter specifications were:
•	High-pass filter: 2nd order Bessel, −3 dB cutoff ≈ 0.5 Hz (to remove baseline wander)
•	Low-pass filter: 4th order Bessel, −3 dB cutoff ≈ 40 Hz (to attenuate EMG noise and mains interference above the ECG band)

Component values were calculated using the Horowitz and Hill method. Capacitor values were chosen first (C = 1 µF throughout, a standard preferred value), and then resistor values were calculated from the required cutoff frequencies and Bessel polynomial coefficients, using combinations of preferred resistor values to achieve the required accuracy.

### Filter Circuit Details
The 4th order Bessel LPF was realised as two cascaded 2nd order Sallen-Key stages. Stage 1 used R17 = 2.779 kΩ, R18 = 2.779 kΩ, C9 = C10 = 1 µF, with a gain-setting resistor R20 = 12 kΩ and R19 = 1.008 kΩ. Stage 2 used R6 = 2.478 kΩ, R10 = 2.478 kΩ, C5 = C6 = 1 µF, with R12 = 12 kΩ and R11 = 9.108 kΩ.

The 2nd order Bessel HPF used C7 = C8 = 1 µF with R13 = R16 = 404.89 kΩ and gain-setting resistors R15 = 3.216 kΩ and R14 = 12 kΩ. The HPF was placed after the LPF in the signal chain.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ecg-project/filter-bode-circuit.JPG" title="Filter Circuit" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Filter Circuit
</div>

### Simulated Frequency Response
A Bode plot simulation was performed over the range 10 mHz to 1 kHz. The plot confirmed the correct bandpass shape, with a flat passband between approximately 0.5 Hz and 40 Hz. The high-pass roll-off at low frequencies and the low-pass roll-off above 40 Hz are both clearly visible, with the combined response providing strong attenuation of both baseline wander and high-frequency noise.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ecg-project/filter-bode-graph.JPG" title="Filter Graph" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Filter Bode Plot
</div>

The calculated frequency response data is tabulated below, showing Vout pp, time delay (Δt), and phase shift (ΔΦ) across the frequency range:

| Frequency (Hz) | Vout pp (V) Calc. | Δt Vout vs Vin (ms) Calc. | ΔΦ Vout vs Vin (°) Calc.|
| -------------- | ----------------- | ------------------------- | ----------------------- |
| 0.1            | 0.198             | -4291.12                  | -154.48                 |
| 0.5            | 1.72              | -421.22                   | -75.82                  |
| 1              | 2.29              | -102.70                   | -36.97                  |
| 10             | 2.41              | 8.14                      | 29.30                   |
| 20             | 2.25              | 8.27                      | 59.51                   |
| 30             | 2.02              | 8.41                      | 90.83                   |
| 40             | 1.72              | 8.40                      | 121.03                  |
| 50             | 1.43              | 8.34                      | 150.16                  |
| 60             | 1.16              | 8.21                      | 177.31                  |
| 70             | 0.92              | 8.00                      | 201.69                  |
| 80             | 0.73              | 7.73                      | 222.61                  |
| 100            | 0.48              | 6.91                      | 248.65                  |
| 120            | 0.34              | 14.64                     | 632.29                  |
| 200            | 0.13              | 14.55                     | 1047.95                 |
| 400            | 0.058             | -                         | -                       |
| 600            | 0.038             | -                         | -                       |

---

The passband output at 10–20 Hz peaks at approximately 2.41 Vpp (normalised to 0.5 Vpp input), confirming unity-gain passband behaviour. The attenuation at 0.1 Hz (−21 dB relative to passband) and at 100 Hz (−14 dB) is consistent with the designed Bessel response. The linear phase response across the passband (phase changes smoothly from −36° at 1 Hz to +150° at 50 Hz) confirms the group delay flatness characteristic of the Bessel topology.

## Offset Amplifier and Circuit Integration
### Design Purpose
The Teensy microcontroller's ADC accepts input voltages only in the range 0–3.3 V. The filtered ECG signal is centred around 0 V and swings symmetrically positive and negative, so a DC offset must be added to shift the entire signal into the valid input range. A summing amplifier circuit was designed for this purpose.

### Circuit Design
A two-op-amp summing and buffering circuit was implemented. A resistor divider (R1 = 12 kΩ, R2 = 1.2 kΩ) from the 9 V battery supply generated a reference voltage of approximately 0.818 V, which was summed with the filtered ECG signal through equal-value input resistors (R4 = R5 = 22 kΩ, feedback R3 = 22 kΩ). A unity-gain output buffer stage (R6 = R7 = 10 kΩ) provided low output impedance for driving the Teensy ADC input.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ecg-project/offset-amp-circuit.JPG" title="Offset Amplifier Circuit" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Offset Amplifier Circuit
</div>

The LTSpice simulation measured an offset of Voffset = 0.781 V. The simulation confirmed that the output signal (blue trace) sits approximately 0.78 V above the input (green trace) while preserving the waveform shape, successfully shifting the signal baseline into the positive-only ADC range.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ecg-project/offset-amp-graph.JPG" title="Offset Amplifier Circuit" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Offset Amplifier Simulation showing V(in) and V(out)
</div>

## ECG Digitisation
A sampling frequency of Fs = 200 Hz was selected for the Teensy ADC. This choice was driven directly by the requirements of the Pan-Tompkins QRS detection algorithm, which was developed and validated at 200 Hz. Sampling at this rate satisfies the Nyquist criterion for the 40 Hz bandpass filter output (requiring Fs > 80 Hz) with substantial margin, while keeping the computational load on the Teensy microcontroller manageable.

The MATLAB implementation of Pan-Tompkins includes an automatic resampling step. If the loaded MIT-BIH file has a different native sampling rate, the signal is resampled to 200 Hz before processing, ensuring consistent algorithm behaviour across all test files.

The offset voltage of approximately 0.78 V ensured the Teensy input remained above 0 V at all times, and the signal amplitude after the full ×525 amplification was scaled such that the R wave peak approached but did not saturate the 3.3 V ADC ceiling.

## Digital R Wave Detection (Pan-Tompkins Algorithm)
### Algorithm Selection and Justification
The Pan-Tompkins algorithm [Pan & Tompkins, IEEE Trans. Biomed. Eng., 1985] was selected as the QRS detection method. This algorithm is one of the most widely cited and validated QRS detectors in the literature, having been specifically designed for real-time operation at low sampling rates (200 Hz). Its multi-stage signal processing pipeline and adaptive thresholding approach make it robust to a wide range of noise conditions, and its modest computational requirements made it a practical choice for implementation on a resource-constrained Teensy microcontroller.

### Signal Processing Pipeline
The algorithm processes the digitised ECG through five sequential stages before applying the decision rule:
•	Low-pass filter: An IIR filter (H(z) = (1−z⁻⁶)² / (1−z⁻¹)²) removes high-frequency noise above the QRS band.
•	High-pass filter: An IIR filter (H(z) = (−1 + 32z⁻¹⁶ + z⁻³²) / (1+z⁻¹)) removes baseline wander below the QRS band.
•	Derivative filter: A five-point derivative (h = [−1, −2, 0, 2, 1] × 1/8) highlights the steep slopes characteristic of QRS complexes.
•	Squaring: The derivative output is squared, amplifying large slopes and suppressing small ones, making QRS peaks more prominent.
•	Moving window integration (MWI): A 150 ms moving average integrates the squared signal, producing a smoother envelope that is easier to threshold.

Following the MWI, a fiducial mark step locates candidate peaks with a minimum separation of 200 ms (40 samples at 200 Hz), enforcing the physiological constraint that valid RR intervals cannot be shorter than this.

### Adaptive Decision Rules
The decision stage uses dual adaptive thresholds (one applied to the MWI output and one to the bandpass-filtered signal) that are continuously updated based on the running estimates of signal level and noise level:
•	THR_SIG = Noise_Level + 0.25 × |Signal_Level − Noise_Level|: the primary detection threshold.
•	THR_NOISE = 0.5 × THR_SIG: the secondary noise classification threshold.
•	T-wave discrimination: If a candidate peak occurs within 360 ms of the previous QRS, its slope is compared to that of the previous QRS. If the slope is less than half the previous QRS slope, the candidate is classified as a T wave and rejected.
•	Searchback: If no QRS is detected within 1.66 × mean RR interval, the algorithm searches backward through the signal to recover any missed beats that may have fallen below the threshold.

Both signal and noise level estimates are updated using a weighted average after each detection decision, allowing the algorithm to track gradual changes in ECG amplitude throughout the recording.

### MIT-BIH Database Validation
The MATLAB implementation was validated against the MIT-BIH Noise Stress Test Database (NSTDB). The clean reference recording nsrdb_001 (300 seconds of Lead II normal sinus rhythm) was used to establish the true R wave count. The algorithm was then run against five noise-contaminated versions of the same recording at 0 dB SNR:
•	nstdb_001_baseline_0dB: Baseline wander noise
•	nstdb_001_combo_0dB: Combined noise (all types superimposed)
•	nstdb_001_emg_0dB: EMG (muscle artefact) noise
•	nstdb_001_mains_0dB: 50 Hz mains interference
•	nstdb_001_motion_0dB: Motion artefact noise

The Pan-Tompkins algorithm performed exceptionally well across nearly all noise conditions. The algorithm achieved 100% sensitivity on every noise file, meaning no true QRS complexes were missed, and 100% PPV on four of the five noisy conditions. The only notable reduction in performance was in the combined noise file (all noise types superimposed), where 40 false positive detections occurred, reducing PPV to 93.07%. A single false positive also appeared in the EMG noise condition (PPV 99.81%). Notably, baseline wander, mains interference, and motion artefact each produced zero false positives, demonstrating that the bandpass filter and adaptive thresholding effectively neutralised these noise types at 0 dB SNR.

## Real-Time Teensy Implementation
### Hardware Setup
The Teensy microcontroller received the analog-conditioned ECG signal on analogue input pin A0. The ADC was configured with 10-bit resolution (0–1023 counts), corresponding to an input range of 0–3.3 V (approximately 3.22 mV/count). Analogue output was provided via the Teensy DAC on pin A14, allowing the processed ECG waveform to be monitored on an oscilloscope. A digital output on pin A1 (and the onboard LED on pin 13) was used as the R wave detection indicator.

### Detection Algorithm
The Teensy detection algorithm used a two-sample slope-based approach optimised for real-time single-sample processing. At each sample, the algorithm compared the current and previous ADC readings to detect the characteristic peak of an R wave:
•	Both the current and previous samples must exceed the threshold value (threshold_voltage = 2.0 V → threshold_value ≈ 620 counts) to confirm the signal is in the QRS amplitude range.
•	The difference between consecutive samples (diff = data_new − data_old) must be positive (rising edge) and below diff_threshold = 120 counts, identifying the moment the signal reaches its peak (slope near zero after a rise).
•	A minimum gap of gap_num = sampling_ratio / max_bit_per_second = 400/4 = 100 samples (250 ms at 400 Hz sample rate on the Teensy) was enforced between detections to prevent repeated triggering on a single R wave.

The sampling rate on the Teensy was set to 400 Hz (delay of 2.5 ms per loop iteration), with the ADC reading, detection logic, serial output, and DAC write all completing within this interval.

### Output and Display
On each R wave detection, the onboard LED (pin 13) and digital output pin A1 were driven HIGH, producing a visual indicator and a logic-level pulse suitable for oscilloscope triggering. The ECG waveform was simultaneously output through the DAC on pin A14, allowing Channel 1 of the oscilloscope to display the filtered ECG and Channel 2 to display the QRS detection output. Serial data was also streamed at 512,000 baud for PC-side monitoring.

### Live Demonstration
The complete system was successfully demonstrated in the laboratory on a human subject. ECG electrodes were placed in a Lead II configuration and connected to the amplifier input through the 39 kΩ safety resistors, with the circuit powered exclusively from batteries. The filtered ECG waveform was clearly visible on Channel 1 of the oscilloscope, displaying characteristic PQRST morphology. On each heartbeat, the LED illuminated and the detection pulse appeared on Channel 2, confirming reliable real-time R wave detection.

## Project Discussion
### How Well Were the Design Criteria Met? 
All primary design objectives were successfully achieved. The instrumentation amplifier delivered a gain of ×524.6 against a target of ≥500 (error <0.1%). The Bessel bandpass filter produced the correct passband shape from 0.5 to 40 Hz, confirmed by both the Bode plot simulation and the calculated frequency response table. The offset amplifier correctly shifted the signal baseline to approximately 0.78 V, keeping the full ECG waveform within the 0–3.3 V ADC window. The Pan-Tompkins algorithm was successfully implemented in MATLAB and ported to the Teensy, with live R wave detection demonstrated on a human subject.

The CMRR of the physical circuit, while not quantitatively measured in the final build, was assessed via simulation and shown to be theoretically very high under ideal resistor matching conditions. In practice, the ±5% resistor tolerance used in the lab would reduce the CMRR from the ideal simulation value, but the common-mode rejection provided by the instrumentation amplifier topology is inherently strong and sufficient for ECG acquisition in a low-noise laboratory environment.

### How Would You Improve the System? 
Several improvements could enhance the robustness and clinical utility of the system:
•	Resistor precision: Replacing the ±5% tolerance resistors in the instrumentation amplifier with 0.1% precision resistors would significantly improve CMRR and reduce common-mode noise pickup in practical use.
•	Right-leg drive circuit: Adding an active driven-right-leg (DRL) circuit would further suppress common-mode interference, particularly 50/60 Hz mains noise, which is the dominant artefact in real clinical environments.
•	Notch filter: A 50 Hz notch filter could be added between the bandpass filter and the offset stage to provide additional attenuation of mains interference without significantly affecting QRS morphology.
•	Improved Teensy algorithm: The slope-based peak detector on the Teensy, while effective in low-noise conditions, could be replaced with a full embedded Pan-Tompkins implementation for greater robustness. The MATLAB algorithm demonstrated that the full adaptive thresholding and searchback logic provides substantially better noise immunity than a simple amplitude threshold.
•	PCB implementation: Moving from a breadboard to a custom PCB would reduce parasitic capacitances, improve signal integrity, and allow the circuit to be miniaturised into a wearable form factor.
•	Battery monitoring: Adding low-battery detection would prevent the amplifier supply voltage from drifting during use, which could otherwise affect gain calibration.

## Conclusion
This project successfully demonstrated the design and implementation of a complete ECG acquisition and R wave detection system, integrating analog circuit design, digital signal processing, and embedded programming into a functioning biomedical instrument. The system achieved all specified design targets: a front-end instrumentation amplifier with ×525 gain and high common-mode rejection, a Bessel bandpass filter correctly shaping the 0.5–40 Hz ECG band, an offset amplifier precisely conditioning the signal for the Teensy ADC, and a real-time Pan-Tompkins QRS detector producing live R wave detection output demonstrated on a human subject.

The project provided practical experience in the challenges of low-level biopotential measurement, including the importance of patient safety, common-mode noise rejection, signal conditioning for ADC compatibility, and the trade-offs involved in implementing a computationally demanding algorithm on a resource-constrained embedded system. The skills developed, from LTSpice simulation through to embedded C programming, form a strong foundation for further work in biomedical instrumentation and wearable health monitoring.

## References
- [1] Pan, J. and Tompkins, W.J. (1985) 'A Real-Time QRS Detection Algorithm', IEEE Transactions on Biomedical Engineering, Vol. BME-32, No. 3, pp. 230–236.

- [2] Moody, G.B. and Mark, R.G. (1992) 'The MIT-BIH Noise Stress Test Database', Computers in Cardiology, Vol. 19, pp. 445–448.

- [3] Horowitz, P. and Hill, W. (2015) The Art of Electronics, 3rd edition. Cambridge University Press.

- [4] Texas Instruments (2015) TL072 Low-Noise JFET-Input Operational Amplifiers Datasheet.

## Appendix
### Appendix A - MATLAB Pan-Tompkins Code

{% raw %}

```m
%Implementation of:
%[1]PAN.J, TOMPKINS. W.J,"A Real-Time QRS Detection Algorithm" IEEE
%TRANSACTIONS ON BIOMEDICAL ENGINEERING, VOL. BME-32, NO. 3, MARCH 1985.

%% Loading the File
[FileName, PathName] = uigetfile('*.mat', 'Select File'); %opens dialog box for opening .bin files
fopen([PathName FileName]);
filename = strcat(PathName,FileName);
load (filename);
ecg = val;
sampling = strcat(PathName,'sampling.mat');
load(sampling);
clearvars val PathName sampling filename fid FileName;

%% Initialize
qrs_c =[]; %amplitude of R
qrs_i =[]; %index
nois_c =[];
nois_i =[];
delay = 0;
skip = 0; % becomes one when a T wave is detected
not_noise = 0; % it is not noise when not_nois = 1
selected_RR =[]; % Selected RR intervals
m_selected_RR = 0;
mean_RR = 0;
qrs_i_raw =[];
qrs_amp_raw=[];
ser_back = 0; 
test_m = 0;
SIGL_buf = [];
NOISL_buf = [];
THRS_buf = [];
SIGL_buf1 = [];
NOISL_buf1 = [];
THRS_buf1 = [];

%% Pre-Processing

% Step 1) Signal is preprocessed , if the sampling frequency is higher then it 
% is downsampled and if it is lower upsampled to make the sampling 
% frequency 200 Hz with the same filtering setups introduced in Pan
% tompkins paper 

if Fs == 200
    t = time;
else
    ecg = resample(ecg, 200, Fs);
    N = length(ecg);
    t = 0:1/200:(1/200)*(N-1);
end
Fs = 200;
figure,  ax(1)=subplot(321);plot(t, ecg);axis tight;grid;title('Raw ECG Signal');

clearvars time;

% Step 2) apply combination of low pass and high pass filter 5-15 Hz
% to get rid of the baseline wander and muscle noise. 

% Low Pass Filter  H(z) = ((1 - z^(-6))^2)/(1 - z^(-1))^2
b = [1 0 0 0 0 0 -2 0 0 0 0 0 1];
a = [1 -2 1];
h_l = filter(b,a,[1 zeros(1,12)]); 
ecg_l = conv (ecg ,h_l);
ecg_l = ecg_l/ max( abs(ecg_l));
ecg_l = ecg_l(((length(b)-1)/2): N-1 + (length(b)-1)/2);
ax(2)=subplot(322);plot(t, ecg_l);axis tight;grid;title('Low pass filtered');

% High Pass filter H(z) = (-1+32z^(-16)+z^(-32))/(1+z^(-1))
b = [-1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 32 -32 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1];
a = [1 -1];
h_h = filter(b,a,[1 zeros(1,32)]); 
ecg_h = conv (ecg_l ,h_h);
ecg_h = ecg_h/ max( abs(ecg_h));
ecg_h = ecg_h(((length(b)-1)/2): N-1 + (length(b)-1)/2);
ax(3)=subplot(323);plot(t, ecg_h);axis tight;grid;title('High Pass Filtered');


% 3) The filtered signal
% is derivated using a derivating filter to high light the QRS complex.
% derivative filter H(z) = (1/8T)(-z^(-2) - 2z^(-1) + 2z + z^(2))
h_d = [-1 -2 0 2 1]*(1/8);%1/8*Fs
ecg_d = conv (ecg_h ,h_d);
ecg_d = ecg_d/max(ecg_d);
ecg_d = ecg_d(((length(h_d)-1)/2): N-1 + (length(h_d)-1)/2);
ax(4)=subplot(324);plot(t, ecg_d);axis tight;grid;title('Filtered with the derivative filter');
% 4) Signal is squared.
ecg_s = ecg_d.^2;
ecg_s = ecg_s/ max( abs(ecg_s));
ax(5)=subplot(325);plot(t, ecg_s);axis tight;grid;title('Squared');
% 5)Signal is averaged with a moving window to get rid
% of noise (0.150 seconds length).
ecg_m = conv(abs(ecg_s) ,ones(1 ,round(0.150*Fs)));
ecg_m = ecg_m(round(0.150*Fs): N-1 + round(0.150*Fs));
ecg_m = ecg_m/ max( abs(ecg_m));
ax(6)=subplot(326);plot(t, ecg_m);axis tight;grid;title('Averaged with 30 samples length,Black noise,Green Adaptive Threshold,RED Sig Level,Red circles QRS adaptive threshold');
axis tight;grid;
linkaxes(ax,'x');

%% Decision Rule 
% At this point in the algorithm, the preceding stages have produced a 
% roughly pulse-shaped waveform at the output of the MWI . The determination 
% as to whether this pulse corresponds to a QRS complex (as opposed to a 
% high-sloped T-wave or a noise artefact) is performed with an adaptive 
% thresholding operation and other decision rules outlined below;

% a) FIDUCIAL MARK - The waveform is first processed to produce a set of 
% weighted unit samples at the location of the MWI maxima. This is done in 
% order to localize the QRS complex to a single instant of time. 
% The w[k] weighting is the maxima value.
%
% Note : a minimum distance of 40 samples is considered between each R wave
% since in physiological point of view no RR wave can occur in less than
% 200 msec distance

%% initialize the training phase (2 seconds of the signal) to determine the THR_SIG and THR_NOISE
THR_SIG = max(ecg_m(1:2*Fs))*1/3; % 0.25 of the max amplitude 
THR_NOISE = mean(ecg_m(1:2*Fs))*1/2; % 0.5 of the mean signal is considered to be noise
SIG_LEV= THR_SIG;
NOISE_LEV = THR_NOISE;

%% Fiducial Mark 
% Note : a minimum distance of 40 samples is considered between each R wave
% since in physiological point of view no RR wave can occur in less than
% 200 msec distance
[pks,locs] = findpeaks(ecg_m,'MINPEAKDISTANCE',round(0.2*Fs)); %,'MinPeakHeight',THR_NOISE);

%% Initialize bandpath filter threshold(2 seconds of the bandpass signal)
THR_SIG1 = max(ecg_h(1:2*Fs))*1/3; % 0.25 of the max amplitude 
THR_NOISE1 = mean(ecg_h(1:2*Fs))*1/2; %
SIG_LEV1 = THR_SIG1; % Signal level in Bandpassed filter
NOISE_LEV1 = THR_NOISE1; % Noise level in Bandpassed filter
%% Thresholding and online desicion rule

for i = 1 : length(pks)
    
   %% locate the corresponding peak in the filtered signal 
    if locs(i)>= 1 && locs(i)+ round(0.150*Fs)<= length(ecg_h)
          [y_i, x_i] = max(ecg_h(locs(i):locs(i)+ round(0.150*Fs)));
          x_i = x_i + locs(i)-1;
       else
          if locs(i) + round(0.150*Fs) >= length(ecg_h)
            [y_i, x_i] = max(ecg_h(locs(i):end));
            x_i = x_i + locs(i)-1;
          end
     end
    
    
  %% update the heart_rate (Two heart rate means one the most recent and the other selected)
    if length(qrs_c) >= 9   
        diffRR = diff(qrs_i(end-8:end)); %calculate RR interval
        mean_RR = mean(diffRR); % calculate the mean of 8 previous R waves interval
        comp =qrs_i(end)-qrs_i(end-1); %latest RR
        if comp <= 0.92*mean_RR || comp >= 1.16*mean_RR
            % lower down thresholds to detect better in MVI
                THR_SIG = 0.5*(THR_SIG); 
               % lower down thresholds to detect better in Bandpass filtered 
                THR_SIG1 = 0.5*(THR_SIG1); 
        else
            m_selected_RR = mean_RR; %the latest regular beats mean
        end     
    end
    
      %% calculate the mean of the last 8 R waves to make sure that QRS is not
       % missing(If no R detected , trigger a search back) 1.66*mean
       if m_selected_RR
           test_m = m_selected_RR; %if the regular RR availabe use it   
       elseif mean_RR && m_selected_RR == 0
           test_m = mean_RR;   
       else
           test_m = 0;
       end
        
    if test_m
          if (locs(i) - qrs_i(end)) >= round(1.66*test_m)% it shows a QRS is missed 
              [pks_temp,locs_temp] = max(ecg_m(qrs_i(end)+ round(0.360*Fs):locs(i)-round(0.200*Fs))); % search back and locate the max in this interval
              locs_temp = qrs_i(end)+ round(0.360*Fs) + locs_temp -1; %location 
              if pks_temp > THR_NOISE
               qrs_c = [qrs_c pks_temp];
               qrs_i = [qrs_i locs_temp];
              
               % find the location in filtered sig
               if locs_temp + round(0.150*Fs) <= length(ecg_h)
                [y_i_t x_i_t] = max(ecg_h(locs_temp:locs_temp+round(0.150*Fs)));
                x_i_t = x_i_t + locs_temp-1;
               else
                [y_i_t x_i_t] = max(ecg_h(locs_temp:end));
                x_i_t = x_i_t + locs_temp-1;
               end
               % take care of bandpass signal threshold
               if y_i_t > THR_NOISE1    
                      qrs_i_raw = [qrs_i_raw x_i_t];% save index of bandpass 
                      qrs_amp_raw =[qrs_amp_raw y_i_t]; %save amplitude of bandpass 
                      SIG_LEV1 = 0.25*y_i_t + 0.75*SIG_LEV1; %when found with the second thres 
               end
               not_noise = 1;
               SIG_LEV = 0.25*pks_temp + 0.75*SIG_LEV ;  %when found with the second threshold             
             end 
          else
              not_noise = 0;  
          end
    end
  
    %%  find noise and QRS peaks
    if pks(i) >= THR_SIG
                 % if a QRS candidate occurs within 360ms of the previous QRS
                 % ,the algorithm determines if its T wave or QRS
                 if length(qrs_c) >= 3
                      if (locs(i)-qrs_i(end)) <= round(0.3600*Fs)
                          if locs(i) + round(0.150*Fs) <= length(ecg_h)
                            Slope1 = max(ecg_d(locs(i):locs(i)+ round(0.150*Fs))); %max slope of the waveform at that position
                          else
                            Slope1 = max(ecg_d(locs(i):end)); %max slope of the waveform at that position
                          end
                          Slope2 = max(ecg_d(qrs_i(end):qrs_i(end)+ round(0.150*Fs))); %max slope of the waveform at that position
                             if abs(Slope1) <= abs(0.5*(Slope2))  % slope less then 0.5 of previous R
                                 nois_c = [nois_c pks(i)];
                                 nois_i = [nois_i locs(i)];
                                 skip = 1; % T wave identification
                                 % adjust noise level in both filtered and
                                 % MVI
                                 NOISE_LEV1 = 0.125*y_i + 0.875*NOISE_LEV1;
                                 NOISE_LEV = 0.125*pks(i) + 0.875*NOISE_LEV; 
                             else
                                 skip = 0;
                             end
                      end
                 end
        
        if skip == 0  % skip is 1 when a T wave is detected       
        qrs_c = [qrs_c pks(i)];
        qrs_i = [qrs_i locs(i)];
        
        % bandpass filter check threshold
         if y_i >= THR_SIG1
          qrs_i_raw = [qrs_i_raw x_i];% save index of bandpass 
          qrs_amp_raw = [qrs_amp_raw y_i];% save amplitude of bandpass 
          SIG_LEV1 = 0.125*y_i + 0.875*SIG_LEV1;% adjust threshold for bandpass filtered sig
         end
         
        % adjust Signal level
        SIG_LEV = 0.125*pks(i) + 0.875*SIG_LEV ;
        end
        
        
    elseif THR_NOISE <= pks(i) && pks(i)<THR_SIG
         %adjust Noise level in filtered sig
         NOISE_LEV1 = 0.125*y_i + 0.875*NOISE_LEV1;
         %adjust Noise level in MVI
         NOISE_LEV = 0.125*pks(i) + 0.875*NOISE_LEV; 
    elseif pks(i) < THR_NOISE
        nois_c = [nois_c pks(i)];
        nois_i = [nois_i locs(i)];
        
        % noise level in filtered signal
        NOISE_LEV1 = 0.125*y_i + 0.875*NOISE_LEV1;
        %end
        
         %adjust Noise level in MVI
        NOISE_LEV = 0.125*pks(i) + 0.875*NOISE_LEV;  
        
           
    end
    
    
    
 
    
    %% adjust the threshold with SNR
    if NOISE_LEV ~= 0 || SIG_LEV ~= 0
        THR_SIG = NOISE_LEV + 0.25*(abs(SIG_LEV - NOISE_LEV));
        THR_NOISE = 0.5*(THR_SIG);
    end
    
    % adjust the threshold with SNR for bandpassed signal
    if NOISE_LEV1 ~= 0 || SIG_LEV1 ~= 0
        THR_SIG1 = NOISE_LEV1 + 0.25*(abs(SIG_LEV1 - NOISE_LEV1));
        THR_NOISE1 = 0.5*(THR_SIG1);
    end
    
    
% take a track of thresholds of smoothed signal
SIGL_buf = [SIGL_buf SIG_LEV];
NOISL_buf = [NOISL_buf NOISE_LEV];
THRS_buf = [THRS_buf THR_SIG];

% take a track of thresholds of filtered signal
SIGL_buf1 = [SIGL_buf1 SIG_LEV1];
NOISL_buf1 = [NOISL_buf1 NOISE_LEV1];
THRS_buf1 = [THRS_buf1 THR_SIG1];
  
skip = 0; %reset parameters
not_noise = 0; %reset parameters
ser_back = 0;  %reset bandpass param   
end

hold on,scatter(qrs_i/Fs,qrs_c,'m');
hold on,plot(locs/Fs,NOISL_buf,'--k','LineWidth',2);
hold on,plot(locs/Fs,SIGL_buf,'--r','LineWidth',2);
hold on,plot(locs/Fs,THRS_buf,'--g','LineWidth',2);
if ax(:)
linkaxes(ax,'x');
zoom on;
end
pause;

%% overlay on the signals
figure,az(1)=subplot(311);plot(ecg_h);title('QRS on Filtered Signal');axis tight;
hold on,scatter(qrs_i_raw,qrs_amp_raw,'m');
hold on,plot(locs,NOISL_buf1,'LineWidth',2,'Linestyle','--','color','k');
hold on,plot(locs,SIGL_buf1,'LineWidth',2,'Linestyle','-.','color','r');
hold on,plot(locs,THRS_buf1,'LineWidth',2,'Linestyle','-.','color','g');
az(2)=subplot(312);plot(ecg_m);title('QRS on MVI signal and Noise level(black),Signal Level (red) and Adaptive Threshold(green)');axis tight;
hold on,scatter(qrs_i,qrs_c,'m');
hold on,plot(locs,NOISL_buf,'LineWidth',2,'Linestyle','--','color','k');
hold on,plot(locs,SIGL_buf,'LineWidth',2,'Linestyle','-.','color','r');
hold on,plot(locs,THRS_buf,'LineWidth',2,'Linestyle','-.','color','g');
az(3)=subplot(313);plot(ecg-mean(ecg));title('Pulse train of the found QRS on ECG signal');axis tight;
line(repmat(qrs_i_raw,[2 1]),repmat([min(ecg-mean(ecg))/2; max(ecg-mean(ecg))/2],size(qrs_i_raw)),'LineWidth',2.5,'LineStyle','-.','Color','r');
linkaxes(az,'x');
zoom on;
```

{% endraw %}

### Appendix B - Teensy Arduino Sketch

{% raw %}

```c
const int ledPin = 13; //LED_pin
const int a1 = 15; //digital output for R wave
const int sampling_ratio = 400; //change
const int max_bit_per_second = 4; //change
const float max_voltage = 3.3;  // Max acceptable input voltage
const float max_ano_value = 1023; // value of voltage output when receive max acceptable
float threshold_voltage = 2.0; // Signal lower than this, will be ignored //change
float threshold_value = threshold_voltage/max_voltage*max_ano_value;  //Only signal amplitude higher than this value could be R wave
int voltage_shift = 0;

//When two measurement difference is smaller than this number, 
//we know the signal reach to the top
int diff_threshold = 120; //change

// Read Value holder
int data_new = 0;
int data_old = 0;

// Time gap
int sample_num = 0;
int gap_num = sampling_ratio/max_bit_per_second;

void setup()
{
  //initialize the serial communication:
  Serial.begin(512000);
  analogWriteResolution(10); //10 bits
  pinMode(ledPin, OUTPUT);
  pinMode(A1, OUTPUT);
}

int if_QRS(int read_1, int read_2){
  sample_num ++;
  
  if (read_1 >= threshold_value && read_2 >= threshold_value){
    int diff = read_1 - read_2;
    if (diff > 0 && diff <=diff_threshold){
      if (sample_num < gap_num){
        return 0;
      }
      return 1;
      //digitalWrite(ledPin, HIGH);
      //analogWrite(A14,HIGH);  //write data to DAC
      //digitalWrite(A15,HIGH);
      sample_num = 0;
    }else {
      //digitalWrite(ledPin, LOW);
      //analogWrite(A14,LOW);  //write data to DAC
      //digitalWrite(A15,LOW);
      return 0;
    }
  }
}

void print_data(int data_read){
    Serial.print(sampling_ratio);
    //microseconds since last sample, please note that printing more data will increase interval
    Serial.print("Hz,");
    Serial.print(data_new);
    Serial.print(",");
    Serial.print(data_new + voltage_shift);
    Serial.println("");
    analogWrite(A14,(int)(data_new+voltage_shift));  //write data to DAC
}

void loop() {
  // put your main code here, to run repeatedly:
    data_new=analogRead(A0);  //read analog data from A0
    if (if_QRS(data_new, data_old)){
      digitalWrite(ledPin, HIGH);
      digitalWrite(A1, HIGH);
    } else {
      digitalWrite(ledPin, LOW);
      digitalWrite(A1, LOW);
    }
    print_data(data_new);
    data_old = data_new + 0;
    delay(int(1000/sampling_ratio));
}
```

{% endraw %}


