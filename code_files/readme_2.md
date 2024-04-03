# Github Code for "Audio equalizer based on FIR filters"
Link Github: <https://github.com/controlpaths/blog/tree/87fbe923b3a888f79df401bee3a0fb8242fb5258/equalizer_fir>

## 1. audio_dft.ipynb
Link: <https://github.com/controlpaths/blog/blob/87fbe923b3a888f79df401bee3a0fb8242fb5258/equalizer_fir/python/audio_dft.ipynb>
### 1.1. Code
```python
import matplotlib.pyplot as plt
from scipy.io import wavfile as wav
from scipy.fft import fft
import numpy as np
#rate, data = wav.read('audiocheck.net_sin_1000Hz_-3dBFS_5s.wav')
rate, data = wav.read('sample3.wav')

window_length = rate;
n_windows = np.floor(len(data)/window_length)
index = 0;

try:
    dataR = [i[0] for i in data]
    print("Stereo File")
except:
    dataR = [i for i in data]
    print("Mono File")
    
dft=[]
for i in range(0,int(n_windows)):
    dft_temp = np.abs(fft(dataR[index:index+window_length]))
    index += window_length
    dft.append(dft_temp)

print("Rate = " + str(rate))
print("Windows =" + str(n_windows))
```
Result
```
Stereo File
Rate = 44100
Windows =105.0
```

This Python code imports necessary libraries for audio processing, reads a WAV file, and computes the Discrete Fourier Transform (DFT) for each window of audio data.

Let's break down the code step by step:

1. **Imports**: The code begins by importing essential libraries:
   - `matplotlib.pyplot` for plotting graphs.
   - `wavfile` from `scipy.io` for reading WAV files.
   - `fft` from `scipy.fft` for computing the Fast Fourier Transform (FFT).
   - `numpy` as `np` for numerical computations.

2. **Reading WAV file**: The line `rate, data = wav.read('sample3.wav')` reads the WAV file named `'sample3.wav'` and stores the sampling rate in the variable `rate` and the audio data in the variable `data`.

3. **Windowing**: The code calculates the length of each window as `window_length = rate` and the number of windows (`n_windows`) by dividing the length of the data by the window length. It then initializes the index to 0 for processing each window.

4. **Check for Stereo or Mono**: It tries to extract the first channel from the audio data. If it fails (which means there's only one channel), it assumes it's a mono file; otherwise, it's a stereo file.

5. **DFT Calculation**: For each window, the code extracts the data for that window (`dataR[index:index+window_length]`), computes the DFT using `fft`, takes the absolute values of the result (`np.abs()`), and appends it to the list `dft`. The index is then incremented by the window length to move to the next window.

6. **Print Results**: Finally, the code prints out the sampling rate (`rate`) and the number of windows (`n_windows`).

**Result Explanation**:
- The code determines that the input audio file is a stereo file because it successfully extracts the first channel of data.
- The sampling rate of the audio file is 44100 samples per second.
- There are approximately 105 windows of audio data, each with a length equal to the sampling rate. This means the audio file is around 2.38 seconds long (105 windows * window length = ~2.38 seconds).

### 1.2. Continuing Code
```python
# Histogram of frequencies
for i in dft:
    plt.plot(i)

plt.xlim(0,rate/2)
plt.xlim(0,6000)
plt.grid()
plt.savefig("audio_dft.svg")
```
Result

![image](/pictures/fig20.png)

This portion of the code generates a histogram of frequencies by plotting the DFT magnitudes for each window of audio data. Let's go through it step by step:

1. **Histogram of Frequencies**:
   - A loop iterates through each DFT result (`i`) in the list `dft`.
   - For each DFT result, a plot is generated using `plt.plot(i)`, where `i` represents the magnitudes of frequency components in the DFT.
   - This loop effectively plots the DFT magnitudes for each window of audio data.

2. **Setting Plot Limits**:
   - `plt.xlim(0, rate/2)` sets the x-axis limits of the plot to range from 0 to half of the sampling rate. This is because the DFT represents frequencies from 0 Hz to the Nyquist frequency (half of the sampling rate).
   - `plt.xlim(0, 6000)` further limits the x-axis to show frequencies up to 6000 Hz. This can be useful for focusing on a specific range of frequencies if the plot covers a wider range.

3. **Grid and Saving Plot**:
   - `plt.grid()` adds a grid to the plot.
   - `plt.savefig("audio_dft.svg")` saves the plot as an SVG file named "audio_dft.svg".

The resulting plot will visualize the distribution of frequency components across the audio signal, providing insights into its spectral content. The x-axis represents frequency in Hertz, and the y-axis represents the magnitude of each frequency component. The grid helps in interpreting the plot more accurately. Finally, the plot is saved as an SVG file named "audio_dft.svg" for further analysis or visualization.

### 1.3. Continuing Code
```python
plt.plot(dataR, label="dataR")
plt.xlim(30000,34000)
plt.legend()
plt.grid()
plt.savefig("audio_signal.svg")
```

```
<ipython-input-15-c3a0b29370df>:5: UserWarning: Creating legend with loc="best" can be slow with large amounts of data.
  plt.savefig("audio_signal.svg")
/usr/lib/python3/dist-packages/IPython/core/pylabtools.py:132: UserWarning: Creating legend with loc="best" can be slow with large amounts of data.
  fig.canvas.print_figure(bytes_io, **kw)
```
![image](/pictures/fig21.png)

This portion of the code generates a plot of the audio signal (`dataR`) over a specific range of samples. Let's break it down:

1. **Plotting the Audio Signal**:
   - `plt.plot(dataR, label="dataR")` plots the audio signal stored in `dataR` with a label "dataR". This signal represents either the left channel of a stereo file or the mono audio data.
   
2. **Setting X-axis Limits**:
   - `plt.xlim(30000, 34000)` sets the x-axis limits of the plot to display only the samples between the 30000th and 34000th index. This focuses on a specific portion of the audio signal.

3. **Adding Legend**:
   - `plt.legend()` adds a legend to the plot, indicating the plotted line is associated with "dataR". This helps in distinguishing different data or signals if there are multiple lines in the plot.

4. **Grid and Saving Plot**:
   - `plt.grid()` adds a grid to the plot, aiding visualization.
   - `plt.savefig("audio_signal.svg")` saves the plot as an SVG file named "audio_signal.svg".

**Warning**:
- The warning message indicates that creating a legend with the location set to "best" can be slow when dealing with large amounts of data. It's a notification to consider using a specific location for the legend if the plotting operation is slow due to the dataset size.

The resulting plot will display a portion of the audio signal, focusing on samples between the 30000th and 34000th index. The legend will indicate that the plotted line represents "dataR", and the grid will aid in visualizing the signal. Finally, the plot is saved as an SVG file named "audio_signal.svg".

## 2. fir_audio.ipynb
Link: <https://github.com/controlpaths/blog/blob/87fbe923b3a888f79df401bee3a0fb8242fb5258/equalizer_fir/python/fir_audio.ipynb>
### 2.1. Code
```python
import numpy as np
from scipy import signal
import matplotlib.pyplot as plt
# global definitions

numTaps = 33
fSample = 44e3
fNyquist = fSample/2

# quantification
nBits = 16
nBitsFrac = 15
# bass filter 
fc_bass = 10
wc_bass = fc_bass/fNyquist
taps_bass = signal.firwin(numTaps,wc_bass, window='boxcar')


print(wc_bass)
```
Result:
```
0.00045454545454545455
```
This Python script appears to be importing necessary libraries such as NumPy, SciPy's signal module, and Matplotlib for signal processing and plotting. Then, it defines some global parameters such as the number of taps (`numTaps`), the sampling frequency (`fSample`), and the Nyquist frequency (`fNyquist`). 

After that, it sets up parameters for quantification, specifying the number of bits (`nBits`) and the number of fractional bits (`nBitsFrac`). These parameters are often used in digital signal processing to represent signal values with a finite precision.

Next, it defines parameters for a bass filter. `fc_bass` represents the cutoff frequency of the bass filter, which is set to 10 Hz in this case. `wc_bass` calculates the normalized cutoff frequency by dividing `fc_bass` by the Nyquist frequency. Then, it generates the filter coefficients (`taps_bass`) using the `signal.firwin()` function from SciPy, which creates a finite impulse response (FIR) filter with a boxcar windowing function.

Finally, it prints the value of `wc_bass`, which represents the normalized cutoff frequency of the bass filter. The printed value is `0.00045454545454545455`.

To sum up, this script sets up parameters for a digital bass filter, calculates the normalized cutoff frequency, generates the filter coefficients, and prints the normalized cutoff frequency value.

### 2.2. Continuing Code
```python
# Quantify bass taps
tapsQ_bass = np.floor(taps_bass * 2**nBitsFrac) / 2**nBitsFrac
print(tapsQ_bass)
sum(tapsQ_bass)
```
Result:
```
[0.03027344 0.03027344 0.03027344 0.03027344 0.03027344 0.03027344
 0.03027344 0.03027344 0.03027344 0.03027344 0.03027344 0.03027344
 0.03027344 0.03027344 0.03027344 0.03027344 0.03030396 0.03027344
 0.03027344 0.03027344 0.03027344 0.03027344 0.03027344 0.03027344
 0.03027344 0.03027344 0.03027344 0.03027344 0.03027344 0.03027344
 0.03027344 0.03027344 0.03027344]
0.999053955078125
```
In this section of the script, the bass filter coefficients (`taps_bass`) are quantified to fit within the specified number of bits (`nBits`) with a given number of fractional bits (`nBitsFrac`). Quantization is a process of reducing the precision of a signal to a specific number of bits, which is common in digital signal processing to represent values with finite precision.

The quantification process involves multiplying the filter coefficients by $2^{nBitsFrac}$, then rounding them down to the nearest integer using `np.floor()`, and finally dividing by $2^{nBitsFrac}$ to bring them back to the original scale.

After quantifying the filter coefficients, the quantized filter coefficients (`tapsQ_bass`) are printed, and then their sum is calculated.

The printed quantized filter coefficients are an array of values. Each value represents a coefficient of the quantized bass filter. 

Finally, the sum of the quantized filter coefficients is calculated, resulting in a value close to 1, which is expected since the sum of the coefficients of a FIR filter typically equals 1. In this case, it's approximately $0.999053955078125$.

In summary, this section quantifies the bass filter coefficients to fit within the specified number of bits and calculates their sum, ensuring that the total response of the filter remains approximately unity.

### 2.3. Continuing Code
```python
w_bass,h_bass = signal.freqz(taps_bass)
wQ_bass,hQ_bass = signal.freqz(tapsQ_bass)
plt.plot(w_bass/np.pi*fNyquist, 20 * np.log10(abs(h_bass)), label='real')
plt.plot(wQ_bass/np.pi*fNyquist, 20 * np.log10(abs(hQ_bass)), label='quantized')
plt.xlim(0,18000)
plt.ylim(-40,1)
plt.legend()
plt.grid()
```
Result:

![image](/pictures/fig22.png)

In this section of the script, the frequency response of both the original and quantized bass filters is computed and plotted. 
- `signal.freqz()` is used to compute the frequency response of the filters. This function calculates the frequency response of a digital filter given its coefficients.
- `w_bass` and `h_bass` store the frequency (in radians/sample) and the complex frequency response of the original bass filter, respectively.
- `wQ_bass` and `hQ_bass` store the frequency and the complex frequency response of the quantized bass filter, respectively.

After computing the frequency responses, the script proceeds to plot them:
- `plt.plot()` is used to plot the frequency responses.
- `w_bass/np.pi*fNyquist` and `wQ_bass/np.pi*fNyquist` convert the frequency from radians/sample to Hz using the Nyquist frequency (`fNyquist`).
- `20 * np.log10(abs(h_bass))` and `20 * np.log10(abs(hQ_bass))` calculate the magnitude responses of the filters in decibels (dB) using the original and quantized frequency responses, respectively.
- `plt.xlim(0,18000)` and `plt.ylim(-40,1)` set the limits of the x-axis (frequency) to 0 Hz to 18 kHz and the y-axis (magnitude) to -40 dB to 1 dB.
- `plt.legend()` adds a legend to the plot to differentiate between the original and quantized filters.
- `plt.grid()` adds a grid to the plot for better readability.
The resulting plot will show the frequency response of the original and quantized bass filters, allowing for visual comparison of their performance in the frequency domain. The x-axis represents frequency, and the y-axis represents magnitude in decibels. The plot will help assess the impact of quantization on the filter's frequency response.

### 2.4. Continuing Code
```python
# fir filter phase
phase = np.arctan(np.imag(hQ_bass)/np.real(hQ_bass))
plt.plot(wQ_bass/np.pi*fNyquist, phase, label='fir phase')
plt.legend()
plt.grid()
plt.savefig("fir_phase.svg")
```
Warning:
```
<ipython-input-15-b9e3acd87f3f>:2: RuntimeWarning: divide by zero encountered in true_divide
  phase = np.arctan(np.imag(hQ_bass)/np.real(hQ_bass))
```
Result:

![image](/pictures/fig23.png)

In this part of the script, the phase response of the quantized FIR filter is computed and plotted.

- `np.arctan(np.imag(hQ_bass)/np.real(hQ_bass))` calculates the phase response of the quantized FIR filter. It uses the inverse tangent function to compute the phase angle, given the imaginary and real parts of the complex frequency response (`hQ_bass`). This calculation is performed element-wise.
- `plt.plot()` is then used to plot the phase response against frequency (`wQ_bass/np.pi*fNyquist`). The x-axis represents frequency in Hz, and the y-axis represents the phase angle in radians.
- `plt.legend()` adds a legend to the plot, labeling the line as the FIR phase.
- `plt.grid()` adds a grid to the plot for better readability.
- `plt.savefig("fir_phase.svg")` saves the plot as an SVG file named "fir_phase.svg".

However, a warning message is displayed:

```
<ipython-input-15-b9e3acd87f3f>:2: RuntimeWarning: divide by zero encountered in true_divide
  phase = np.arctan(np.imag(hQ_bass)/np.real(hQ_bass))
```

This warning suggests that there were divisions by zero encountered during the calculation of the phase response. This can occur when the real part of the complex frequency response is zero, leading to undefined phase angles.

To handle this warning, you might need to adjust the calculation of the phase response to avoid divisions by zero or handle these cases appropriately in your code. For example, you could check for zero values in the real part before performing the division, or you could use a different method to calculate the phase response.

### 2.5. Continuing Code
```python
# bass instantiation template

for i in range (0, numTaps):
    if (taps_bass[i] >= 0):
        print(".b"+str(i)+"("+str(nBits)+"'d"+str(int(taps_bass[i]*2**nBitsFrac))+"),")
    else:
        print(".b"+str(i)+"(-"+str(nBits)+"'d"+str(int(abs(taps_bass[i])*2**nBitsFrac))+"),")
```
Result:
```
.b0(16'd992),
.b1(16'd992),
.b2(16'd992),
.b3(16'd992),
.b4(16'd992),
.b5(16'd992),
.b6(16'd992),
.b7(16'd992),
.b8(16'd992),
.b9(16'd992),
.b10(16'd992),
.b11(16'd992),
.b12(16'd992),
.b13(16'd992),
.b14(16'd992),
.b15(16'd992),
.b16(16'd993),
.b17(16'd992),
.b18(16'd992),
.b19(16'd992),
.b20(16'd992),
.b21(16'd992),
.b22(16'd992),
.b23(16'd992),
.b24(16'd992),
.b25(16'd992),
.b26(16'd992),
.b27(16'd992),
.b28(16'd992),
.b29(16'd992),
.b30(16'd992),
.b31(16'd992),
.b32(16'd992),
```
This section of the script generates an instantiation template for implementing the quantized bass filter coefficients in a hardware description language (HDL) environment, such as Verilog or VHDL.

The code iterates through each coefficient of the bass filter (`taps_bass`) and prints out the corresponding instantiation code. The goal is to represent each coefficient in fixed-point format suitable for the specified number of bits (`nBits`) and fractional bits (`nBitsFrac`). Here's a breakdown of what the code does:

- It loops through each coefficient index from 0 to `numTaps - 1`.
- For each coefficient, it checks if it's positive or negative.
- If the coefficient is positive or zero, it prints the coefficient in fixed-point format using the specified number of bits and fractional bits. The coefficient is multiplied by $2^{nBitsFrac}$ to scale it and then converted to an integer.
- If the coefficient is negative, it prints the coefficient in two's complement form by negating it and then following the same procedure as for positive coefficients.

The resulting output provides the instantiation code for each coefficient of the bass filter in the desired fixed-point format suitable for hardware implementation. Each line corresponds to an individual coefficient, with its index (`i`) represented as `.b{i}`, followed by the fixed-point representation of the coefficient.

In summary, this code segment assists in generating hardware-friendly instantiation code for implementing the quantized bass filter coefficients in an HDL environment, ensuring compatibility with the specified number of bits and fractional bits.

### 2.6. Continuing Code
```python
# mid filter 

fc1_mid = 800
wc1_mid = fc1_mid/fNyquist
fc2_mid = 3000
wc2_mid = fc2_mid/fNyquist
taps_mid = signal.firwin(numTaps,[wc1_mid, wc2_mid], window='hamming', pass_zero=False)
```
```verilog
# Quantify mid taps
tapsQ_mid = np.floor(taps_mid * 2**nBitsFrac) / 2**nBitsFrac
print(tapsQ_mid)
sum(tapsQ_mid)
```
Result:
```
[-0.00094604 -0.00219727 -0.00457764 -0.00869751 -0.01470947 -0.02194214
 -0.02877808 -0.03286743 -0.03149414 -0.02249146 -0.00479126  0.02075195
  0.05154419  0.08325195  0.11090088  0.12976074  0.13644409  0.12976074
  0.11090088  0.08325195  0.05154419  0.02075195 -0.00479126 -0.02249146
 -0.03149414 -0.03286743 -0.02877808 -0.02194214 -0.01470947 -0.00869751
 -0.00457764 -0.00219727 -0.00094604]

0.581878662109375
```
In this section of the script, a mid filter is defined. The mid filter is designed to have a passband ranging from 800 Hz to 3000 Hz. The steps involved are as follows:

1. **Define Filter Parameters**:
   - `fc1_mid`: Lower cutoff frequency of the mid filter, set to 800 Hz.
   - `wc1_mid`: Normalized lower cutoff frequency, calculated by dividing `fc1_mid` by the Nyquist frequency `fNyquist`.
   - `fc2_mid`: Upper cutoff frequency of the mid filter, set to 3000 Hz.
   - `wc2_mid`: Normalized upper cutoff frequency, calculated by dividing `fc2_mid` by `fNyquist`.
   - `taps_mid`: Generate filter coefficients for the mid filter using the `signal.firwin()` function from SciPy. This function designs a finite impulse response (FIR) filter using a Hamming window, ensuring attenuation outside the specified frequency range. The `pass_zero` parameter is set to `False` to create a bandpass filter.

2. **Quantification**:
   - Quantize the mid filter coefficients (`taps_mid`) to fit within the specified number of bits (`nBits`) with the given number of fractional bits (`nBitsFrac`). This process ensures that the coefficients can be represented with finite precision suitable for digital signal processing applications.
   - `tapsQ_mid`: Quantized mid filter coefficients obtained by multiplying the original coefficients by $2^{nBitsFrac}$, flooring the result to the nearest integer, and then dividing by $2^{nBitsFrac}$ to maintain the fractional bits.

3. **Print and Summarize**:
   - Print the quantized mid filter coefficients (`tapsQ_mid`).
   - Calculate the sum of the quantized mid filter coefficients, which is $0.581878662109375$ in this case.

The quantized coefficients and their sum provide insights into the representation of the mid filter in fixed-point format and its overall magnitude. These coefficients can be further used for digital signal processing tasks, such as filtering audio signals within the specified frequency range.

### 2.7. Continuing Code
```python
w_mid,h_mid = signal.freqz(taps_mid)
wQ_mid,hQ_mid = signal.freqz(tapsQ_mid)
plt.plot(w_mid/np.pi*fNyquist, 20 * np.log10(abs(h_mid)), label='real')
plt.plot(wQ_mid/np.pi*fNyquist, 20 * np.log10(abs(hQ_mid)), label='quantized')
plt.xlim(0,18000)
plt.ylim(-40,1)
plt.legend()
plt.grid()
```
![image](/pictures/fig24.png)

This portion of the script computes and plots the frequency response of both the original and quantized mid filters.

- `signal.freqz()` is employed to compute the frequency response of the filters. This function calculates the frequency response of a digital filter given its coefficients.
- `w_mid` and `h_mid` store the frequency (in radians/sample) and the complex frequency response of the original mid filter, respectively.
- `wQ_mid` and `hQ_mid` store the frequency and the complex frequency response of the quantized mid filter, respectively.

Following this, the script plots the frequency responses:

- `plt.plot()` is used to plot the frequency responses.
- `w_mid/np.pi*fNyquist` and `wQ_mid/np.pi*fNyquist` convert the frequency from radians/sample to Hz using the Nyquist frequency (`fNyquist`).
- `20 * np.log10(abs(h_mid))` and `20 * np.log10(abs(hQ_mid))` calculate the magnitude responses of the filters in decibels (dB) using the original and quantized frequency responses, respectively.
- `plt.xlim(0,18000)` and `plt.ylim(-40,1)` set the limits of the x-axis (frequency) to 0 Hz to 18 kHz and the y-axis (magnitude) to -40 dB to 1 dB.
- `plt.legend()` adds a legend to the plot to differentiate between the original and quantized filters.
- `plt.grid()` adds a grid to the plot for better readability.

This plot enables visual comparison of the frequency responses of the original and quantized mid filters, aiding in assessing the impact of quantization on the filter's frequency characteristics. The x-axis represents frequency, and the y-axis represents magnitude in decibels. The plot allows for understanding how quantization affects the filter's frequency response within the specified frequency range.

### 2.8. Continuing Code
```python
# mid instantiation template

for i in range (0, numTaps):
    if (taps_mid[i] >= 0):
        print(".b"+str(i)+"("+str(nBits)+"'d"+str(int(taps_mid[i]*2**nBitsFrac))+"),")
    else:
        print(".b"+str(i)+"(-"+str(nBits)+"'d"+str(int(abs(taps_mid[i])*2**nBitsFrac))+"),")
```
Result:
```
.b0(-16'd30),
.b1(-16'd71),
.b2(-16'd149),
.b3(-16'd284),
.b4(-16'd481),
.b5(-16'd718),
.b6(-16'd942),
.b7(-16'd1076),
.b8(-16'd1031),
.b9(-16'd736),
.b10(-16'd156),
.b11(16'd680),
.b12(16'd1689),
.b13(16'd2728),
.b14(16'd3634),
.b15(16'd4252),
.b16(16'd4471),
.b17(16'd4252),
.b18(16'd3634),
.b19(16'd2728),
.b20(16'd1689),
.b21(16'd680),
.b22(-16'd156),
.b23(-16'd736),
.b24(-16'd1031),
.b25(-16'd1076),
.b26(-16'd942),
.b27(-16'd718),
.b28(-16'd481),
.b29(-16'd284),
.b30(-16'd149),
.b31(-16'd71),
.b32(-16'd30),
```
This section of the script generates an instantiation template for implementing the mid filter coefficients in a hardware description language (HDL) environment, such as Verilog or VHDL.

The code iterates through each coefficient of the mid filter (`taps_mid`) and prints out the corresponding instantiation code. The goal is to represent each coefficient in fixed-point format suitable for the specified number of bits (`nBits`) with the given number of fractional bits (`nBitsFrac`). Here's a breakdown of what the code does:

1. **Iteration through Coefficients**:
   - It loops through each coefficient index from 0 to `numTaps - 1`.

2. **Handling Positive and Negative Coefficients**:
   - For each coefficient, it checks if it's positive or negative.
   - If the coefficient is positive or zero, it prints the coefficient in fixed-point format using the specified number of bits and fractional bits. The coefficient is multiplied by $2^{nBitsFrac}$ to scale it and then converted to an integer.
   - If the coefficient is negative, it prints the coefficient in two's complement form by negating it and then following the same procedure as for positive coefficients.

The resulting output provides the instantiation code for each coefficient of the mid filter in the desired fixed-point format suitable for hardware implementation. Each line corresponds to an individual coefficient, with its index (`i`) represented as `.b{i}`, followed by the fixed-point representation of the coefficient.

This instantiation template facilitates the implementation of the mid filter coefficients in an HDL environment, ensuring compatibility with the specified number of bits and fractional bits and enabling efficient hardware implementation of the filter.

### 2.9. Continuing Code
```python
# treble filter 

fc1_treb = 2000
wc1_treb = fc1_treb/fNyquist
fc2_treb = 8000
wc2_treb = fc2_treb/fNyquist
taps_treb = signal.firwin(numTaps,[wc1_treb, wc2_treb], window='hanning', pass_zero=False)
```
```python
# Quantify treble taps

tapsQ_treb = np.floor(taps_treb * 2**nBitsFrac) / 2**nBitsFrac
print(tapsQ_treb)
sum(tapsQ_treb)
```
Result:
```
[ 0.00000000e+00 -3.05175781e-05  3.96728516e-04  2.62451172e-03
  4.57763672e-03 -3.05175781e-05 -1.15966797e-02 -1.82800293e-02
 -9.33837891e-03  2.13623047e-03 -1.63269043e-02 -7.49816895e-02
 -1.27655029e-01 -9.97314453e-02  3.25622559e-02  1.95800781e-01
  2.69775391e-01  1.95800781e-01  3.25622559e-02 -9.97314453e-02
 -1.27655029e-01 -7.49816895e-02 -1.63269043e-02  2.13623047e-03
 -9.33837891e-03 -1.82800293e-02 -1.15966797e-02 -3.05175781e-05
  4.57763672e-03  2.62451172e-03  3.96728516e-04 -3.05175781e-05
  0.00000000e+00]

0.030029296875
```
In this section of the script, a treble filter is defined. The treble filter is designed to have a passband ranging from 2000 Hz to 8000 Hz. Here's a breakdown of the steps involved:

1. **Define Filter Parameters**:
   - `fc1_treb`: Lower cutoff frequency of the treble filter, set to 2000 Hz.
   - `wc1_treb`: Normalized lower cutoff frequency, calculated by dividing `fc1_treb` by the Nyquist frequency `fNyquist`.
   - `fc2_treb`: Upper cutoff frequency of the treble filter, set to 8000 Hz.
   - `wc2_treb`: Normalized upper cutoff frequency, calculated by dividing `fc2_treb` by `fNyquist`.
   - `taps_treb`: Generate filter coefficients for the treble filter using the `signal.firwin()` function from SciPy. This function designs a finite impulse response (FIR) filter using a Hanning window, ensuring attenuation outside the specified frequency range. The `pass_zero` parameter is set to `False` to create a bandpass filter.

2. **Quantification**:
   - Quantize the treble filter coefficients (`taps_treb`) to fit within the specified number of bits (`nBits`) with the given number of fractional bits (`nBitsFrac`). This process ensures that the coefficients can be represented with finite precision suitable for digital signal processing applications.
   - `tapsQ_treb`: Quantized treble filter coefficients obtained by multiplying the original coefficients by $2^{nBitsFrac}$, flooring the result to the nearest integer, and then dividing by $2^{nBitsFrac}$ to maintain the fractional bits.

3. **Print and Summarize**:
   - Print the quantized treble filter coefficients (`tapsQ_treb`).
   - Calculate the sum of the quantized treble filter coefficients, which is $0.030029296875$ in this case.

The quantized coefficients and their sum provide insights into the representation of the treble filter in fixed-point format and its overall magnitude. These coefficients can be further used for digital signal processing tasks, such as filtering audio signals within the specified frequency range.

### 2.10. Continuing Code
```python
w_treb,h_treb = signal.freqz(taps_treb)
wQ_treb,hQ_treb = signal.freqz(tapsQ_treb)
plt.plot(w_treb/np.pi*fNyquist, 20 * np.log10(abs(h_treb)), label='real')
plt.plot(wQ_treb/np.pi*fNyquist, 20 * np.log10(abs(hQ_treb)), label='quantized')
plt.xlim(0,18000)
plt.ylim(-40,1)
plt.legend()
plt.grid()
```
![image](/pictures/fig25.png)

This section of the script computes and plots the frequency response of both the original and quantized treble filters.

- `signal.freqz()` is used to compute the frequency response of the filters. This function calculates the frequency response of a digital filter given its coefficients.
- `w_treb` and `h_treb` store the frequency (in radians/sample) and the complex frequency response of the original treble filter, respectively.
- `wQ_treb` and `hQ_treb` store the frequency and the complex frequency response of the quantized treble filter, respectively.

After computing the frequency responses, the script proceeds to plot them:

- `plt.plot()` is used to plot the frequency responses.
- `w_treb/np.pi*fNyquist` and `wQ_treb/np.pi*fNyquist` convert the frequency from radians/sample to Hz using the Nyquist frequency (`fNyquist`).
- `20 * np.log10(abs(h_treb))` and `20 * np.log10(abs(hQ_treb))` calculate the magnitude responses of the filters in decibels (dB) using the original and quantized frequency responses, respectively.
- `plt.xlim(0,18000)` and `plt.ylim(-40,1)` set the limits of the x-axis (frequency) to 0 Hz to 18 kHz and the y-axis (magnitude) to -40 dB to 1 dB.
- `plt.legend()` adds a legend to the plot to differentiate between the original and quantized filters.
- `plt.grid()` adds a grid to the plot for better readability.

This plot allows for visual comparison of the frequency responses of the original and quantized treble filters, aiding in assessing the impact of quantization on the filter's frequency characteristics. The x-axis represents frequency, and the y-axis represents magnitude in decibels. The plot helps understand how quantization affects the filter's frequency response within the specified frequency range.

### 2.11. Continuing Code
```python
# treble instantiation template

for i in range (0, numTaps):
    if (taps_treb[i] >= 0):
        print(".b"+str(i)+"("+str(nBits)+"'d"+str(int(taps_treb[i]*2**nBitsFrac))+"),")
    else:
        print(".b"+str(i)+"(-"+str(nBits)+"'d"+str(int(abs(taps_treb[i])*2**nBitsFrac))+"),")
```
Result:
```
.b0(16'd0),
.b1(-16'd0),
.b2(16'd13),
.b3(16'd86),
.b4(16'd150),
.b5(-16'd0),
.b6(-16'd379),
.b7(-16'd598),
.b8(-16'd305),
.b9(16'd70),
.b10(-16'd534),
.b11(-16'd2456),
.b12(-16'd4182),
.b13(-16'd3267),
.b14(16'd1067),
.b15(16'd6416),
.b16(16'd8840),
.b17(16'd6416),
.b18(16'd1067),
.b19(-16'd3267),
.b20(-16'd4182),
.b21(-16'd2456),
.b22(-16'd534),
.b23(16'd70),
.b24(-16'd305),
.b25(-16'd598),
.b26(-16'd379),
.b27(-16'd0),
.b28(16'd150),
.b29(16'd86),
.b30(16'd13),
.b31(-16'd0),
.b32(16'd0),
```
This section of the script generates an instantiation template for implementing the treble filter coefficients in a hardware description language (HDL) environment, such as Verilog or VHDL.

The code iterates through each coefficient of the treble filter (`taps_treb`) and prints out the corresponding instantiation code. The goal is to represent each coefficient in fixed-point format suitable for the specified number of bits (`nBits`) with the given number of fractional bits (`nBitsFrac`). Here's a breakdown of what the code does:

1. **Iteration through Coefficients**:
   - It loops through each coefficient index from 0 to `numTaps - 1`.

2. **Handling Positive and Negative Coefficients**:
   - For each coefficient, it checks if it's positive or negative.
   - If the coefficient is positive or zero, it prints the coefficient in fixed-point format using the specified number of bits and fractional bits. The coefficient is multiplied by $2^{nBitsFrac}$ to scale it and then converted to an integer.
   - If the coefficient is negative, it prints the coefficient in two's complement form by negating it and then following the same procedure as for positive coefficients.

The resulting output provides the instantiation code for each coefficient of the treble filter in the desired fixed-point format suitable for hardware implementation. Each line corresponds to an individual coefficient, with its index (`i`) represented as `.b{i}`, followed by the fixed-point representation of the coefficient.

This instantiation template facilitates the implementation of the treble filter coefficients in an HDL environment, ensuring compatibility with the specified number of bits and fractional bits and enabling efficient hardware implementation of the filter.

### 2.12. Continuing Code
```python
# entire equalizer bode
plt.plot(wQ_bass/np.pi*fNyquist, 20 * np.log10(abs(hQ_bass)), label='bass')
plt.plot(wQ_mid/np.pi*fNyquist, 20 * np.log10(abs(hQ_mid)), label='mid')
plt.plot(wQ_treb/np.pi*fNyquist, 20 * np.log10(abs(hQ_treb)), label='treb')
plt.xlim(0,18000)
plt.ylim(-40,10)
plt.legend()
plt.grid()
plt.savefig("audio_equalizer_fir_bode.svg")
```
![image](/pictures/fig26.png)

This section of the script plots the frequency response of the entire equalizer, which comprises the bass, mid, and treble filters. Here's an explanation of the code:

- `plt.plot()` is used to plot the frequency responses of the bass, mid, and treble filters.
- `wQ_bass/np.pi*fNyquist`, `wQ_mid/np.pi*fNyquist`, and `wQ_treb/np.pi*fNyquist` convert the frequency from radians/sample to Hz using the Nyquist frequency (`fNyquist`).
- `20 * np.log10(abs(hQ_bass))`, `20 * np.log10(abs(hQ_mid))`, and `20 * np.log10(abs(hQ_treb))` calculate the magnitude responses of the filters in decibels (dB) using the quantized frequency responses of the bass, mid, and treble filters, respectively.
- `plt.xlim(0,18000)` sets the limits of the x-axis (frequency) to 0 Hz to 18 kHz.
- `plt.ylim(-40,10)` sets the limits of the y-axis (magnitude) to -40 dB to 10 dB.
- `plt.legend()` adds a legend to the plot, labeling each line with its corresponding filter name (bass, mid, treble).
- `plt.grid()` adds a grid to the plot for better readability.
- `plt.savefig("audio_equalizer_fir_bode.svg")` saves the plot as an SVG file named "audio_equalizer_fir_bode.svg".

This plot provides a comprehensive view of the frequency response of the entire equalizer system, showing the combined effect of the bass, mid, and treble filters. The x-axis represents frequency in Hz, and the y-axis represents magnitude in decibels. It allows for understanding how the equalizer modifies the magnitude of different frequency components of an audio signal across the audible frequency range.

## 3. iir_example.ipynb
Link: <https://github.com/controlpaths/blog/blob/87fbe923b3a888f79df401bee3a0fb8242fb5258/equalizer_fir/python/iir_example.ipynb>
### 3.1. Code
```python
import numpy as np
from scipy import signal
import matplotlib.pyplot as plt

# global definitions
norder = 2;
fSample = 44e3
fNyquist = fSample/2
fc = 200;
wc = fc/fNyquist

# quantification
nBits = 16
nBitsFrac = 15

# iir filter design
b,a = signal.butter(norder,wc)
w,h = signal.freqz(b,a)
plt.plot(w/np.pi*fNyquist, 20 * np.log10(abs(h)), label='module')
plt.xlim(0,1800)
plt.ylim(-40,1)
plt.legend()
plt.grid()
```
![image](/pictures/fig27.png)

This code is a Python script that demonstrates the design of an Infinite Impulse Response (IIR) filter using the Butterworth filter design method. Let's break down the code step by step:

1. **Importing Libraries**: The first few lines import necessary libraries:
   - `numpy` is imported with the alias `np`. It's a library for numerical computing in Python.
   - `signal` from `scipy` library is imported, which provides signal processing functions.
   - `matplotlib.pyplot` is imported with the alias `plt`. It's a plotting library used for visualizations.

2. **Global Definitions**: 
   - `norder` is set to 2, indicating the order of the Butterworth filter.
   - `fSample` is set to 44,000 Hz, denoting the sampling frequency.
   - `fNyquist` is calculated as half of the sampling frequency, representing the Nyquist frequency.
   - `fc` is set to 200 Hz, which is the cutoff frequency of the filter.
   - `wc` is calculated as the ratio of the cutoff frequency to the Nyquist frequency, determining the normalized cutoff frequency.

3. **Quantification**:
   - `nBits` is set to 16, indicating the total number of bits for quantization.
   - `nBitsFrac` is set to 15, representing the number of fractional bits.

4. **IIR Filter Design**:
   - `signal.butter(norder, wc)` is used to design a Butterworth filter of order `norder` with a normalized cutoff frequency `wc`. This function returns the numerator (`b`) and denominator (`a`) coefficients of the filter transfer function.
   - `signal.freqz(b, a)` computes the frequency response of the filter defined by the coefficients `b` and `a`. It returns the frequencies (`w`) and the complex frequency response (`h`).
   - `plt.plot(w/np.pi*fNyquist, 20 * np.log10(abs(h)), label='module')` plots the frequency response of the filter. It plots the frequencies converted to Hertz (`w/np.pi*fNyquist`), the magnitude response in decibels (`20 * np.log10(abs(h))`), and labels it as 'module'.
   - `plt.xlim(0, 1800)` sets the limits for the x-axis (frequency) from 0 to 1800 Hz.
   - `plt.ylim(-40, 1)` sets the limits for the y-axis (magnitude response in dB) from -40 to 1 dB.
   - `plt.legend()` adds a legend to the plot.
   - `plt.grid()` adds a grid to the plot.

Overall, this code sets up a Butterworth filter with specific parameters, computes its frequency response, and plots the magnitude response in decibels over a specified frequency range.

### 3.2. Continuing Code
```python
# iir filter phase
phase = np.arctan(np.imag(h)/np.real(h))
plt.plot(w/np.pi*fNyquist, phase, label='iir_phase')
plt.legend()
plt.grid()
plt.savefig("iir_phase.svg")
```
![image](/pictures/fig28.png)

This additional code segment calculates and plots the phase response of the previously designed IIR filter. Let's break down the code:

1. **Phase Calculation**:
   - `phase = np.arctan(np.imag(h)/np.real(h))`: This line calculates the phase response of the filter. It uses the `np.arctan()` function to compute the arctangent of the ratio of the imaginary part to the real part of the complex frequency response `h`. This operation yields the phase angle for each frequency point.

2. **Plotting Phase Response**:
   - `plt.plot(w/np.pi*fNyquist, phase, label='iir_phase')`: This line plots the phase response of the filter. It uses the frequencies converted to Hertz (`w/np.pi*fNyquist`) on the x-axis and the calculated phase angles (`phase`) on the y-axis. It labels this plot as 'iir_phase'.
   
3. **Customization and Saving the Plot**:
   - `plt.legend()`: Adds a legend to the plot.
   - `plt.grid()`: Adds a grid to the plot.
   - `plt.savefig("iir_phase.svg")`: Saves the plot as a vector graphics file named "iir_phase.svg". This function saves the current figure in the plot, enabling you to access it later for further analysis or inclusion in documents.

Overall, this code segment extends the previous script by calculating and visualizing the phase response of the IIR filter. The resulting plot provides insights into how the filter affects the phase of the input signal across different frequencies.

## 4. 
```verilog
/**
  Module name:  top_equalizer
  Author: P Trujillo (pablo@controlpaths.com)
  Date: Jun 2021
  Description: Top module to test the i2s modules.
  Revision: 1.0 Module created
**/

module top_equalizer (
  input clk100mhz, 
  
  input [3:0] freq_sw,
  output [3:0] led,
  /* i2s tx interface */
  output tx_mclk,
  output tx_lrck,
  output tx_sclk,
  output tx_data,
  /* i2s rx interface */
  output rx_mclk,
  (*MARK_DEBUG="true"*) output rx_lrck,
  output rx_sclk,
  input rx_data
);

  (*MARK_DEBUG="true"*) wire [63:0] i2s_data_rx; /* i2s data stream received */
  wire [63:0] i2s_data_tx; /* i2s data stream to send */

  (*MARK_DEBUG="true"*) wire [23:0] i2s_ldata; /* l data received */
  (*MARK_DEBUG="true"*) wire [23:0] i2s_rdata; /* r data received */

  wire [23:0] i2s_ldata_bass; /* l data received filtered bass */
  wire [23:0] i2s_ldata_mid; /* l data received filtered mid */
  wire [23:0] i2s_rdata_bass; /* r data received filtered bass */
  wire [23:0] i2s_rdata_mid; /* r data received filtered mid */

  wire [23:0] i2s_ldata_fir_acc; /* data equalized */
  wire [23:0] i2s_rdata_fir_acc; /* data equalized */

  wire i2s_rx_tvalid; /* i2s data valid */

  wire clk25mhz;
  wire resetn;

  assign led[0] = freq_sw[0];

  clk_wiz_0 clk_wiz_inst (
  .clk25mhz(clk25mhz),     
  .locked(resetn),       
  .clk100mhz(clk100mhz)
  ); 

  axis_i2s_rx_v1_0 i2s_rx_inst0(
  .aclk(clk25mhz), 
  .resetn(resetn),
  /* master axis interface */
  .m_axis_tdata(i2s_data_rx), /* format = [l_data, 8'd0, r_data, 8'd0] */
  .m_axis_tlast(),
  .m_axis_tvalid(i2s_rx_tvalid),
  .m_axis_tready(), 
  /* i2s interface */
  .mclk(rx_mclk),
  .lrck(rx_lrck),
  .sclk(rx_sclk),
  .sdin(rx_data)
  );

  assign i2s_ldata = i2s_data_rx[63-:24];
  assign i2s_rdata = i2s_data_rx[31-:24];

  /* left side */
  axis_fir1_32_symm_v1_0 #(
  .inout_width(24),
  .inout_decimal_width(23),
  .coefficient_width(24),
  .coefficient_decimal_width(23),
  .internal_width(24),
  .internal_decimal_width(23)
  ) fir_mid_l (
  .aclk(clk25mhz),
  .resetn(resetn),
  /* slave axis interface */
  .s_axis_tdata(i2s_ldata),
  .s_axis_tlast(1'b0),
  .s_axis_tvalid(i2s_rx_tvalid),
  .s_axis_tready(),
  /* master axis interface */
  .m_axis_tdata(i2s_ldata_mid),
  .m_axis_tlast(),
  .m_axis_tvalid(),
  .m_axis_tready(),
  /* coefficients */
  .b0(-24'd368824),
  .b1(-24'd349467),
  .b2(-24'd314751),
  .b3(-24'd264874),
  .b4(-24'd200762),
  .b5(-24'd124056),
  .b6(-24'd37055),
  .b7(24'd57365),
  .b8(24'd155883),
  .b9(24'd254865),
  .b10(24'd350538),
  .b11(24'd439160),
  .b12(24'd517196),
  .b13(24'd581489),
  .b14(24'd629408),
  .b15(24'd658978),
  .b16(24'd668973)
  );

  axis_fir1_32_symm_v1_0 #(
  .inout_width(24),
  .inout_decimal_width(23),
  .coefficient_width(24),
  .coefficient_decimal_width(23),
  .internal_width(24),
  .internal_decimal_width(23)
  ) fir_bass_l (
  .aclk(clk25mhz),
  .resetn(resetn),
  /* slave axis interface */
  .s_axis_tdata(i2s_ldata),
  .s_axis_tlast(1'b0),
  .s_axis_tvalid(i2s_rx_tvalid),
  .s_axis_tready(),
  /* master axis interface */
  .m_axis_tdata(i2s_ldata_bass),
  .m_axis_tlast(),
  .m_axis_tvalid(),
  .m_axis_tready(),
  /* coefficients */
  .b0(24'd224240),
  .b1(24'd229645),
  .b2(24'd234769),
  .b3(24'd239599),
  .b4(24'd244122),
  .b5(24'd248326),
  .b6(24'd252203),
  .b7(24'd255740),
  .b8(24'd258930),
  .b9(24'd261764),
  .b10(24'd264235),
  .b11(24'd266337),
  .b12(24'd268064),
  .b13(24'd269412),
  .b14(24'd270377),
  .b15(24'd270957),
  .b16(24'd271151)
  );

  /* right side */
  axis_fir1_32_symm_v1_0 #(
  .inout_width(24),
  .inout_decimal_width(23),
  .coefficient_width(24),
  .coefficient_decimal_width(23),
  .internal_width(24),
  .internal_decimal_width(23)
  ) fir_mid_r (
  .aclk(clk25mhz),
  .resetn(resetn),
  /* slave axis interface */
  .s_axis_tdata(i2s_rdata),
  .s_axis_tlast(1'b0),
  .s_axis_tvalid(i2s_rx_tvalid),
  .s_axis_tready(),
  /* master axis interface */
  .m_axis_tdata(i2s_rdata_mid),
  .m_axis_tlast(),
  .m_axis_tvalid(),
  .m_axis_tready(),
  /* coefficients */
  .b0(-24'd368824),
  .b1(-24'd349467),
  .b2(-24'd314751),
  .b3(-24'd264874),
  .b4(-24'd200762),
  .b5(-24'd124056),
  .b6(-24'd37055),
  .b7(24'd57365),
  .b8(24'd155883),
  .b9(24'd254865),
  .b10(24'd350538),
  .b11(24'd439160),
  .b12(24'd517196),
  .b13(24'd581489),
  .b14(24'd629408),
  .b15(24'd658978),
  .b16(24'd668973)
  );

  axis_fir1_32_symm_v1_0 #(
  .inout_width(24),
  .inout_decimal_width(23),
  .coefficient_width(24),
  .coefficient_decimal_width(23),
  .internal_width(24),
  .internal_decimal_width(23)
  ) fir_bass_r (
  .aclk(clk25mhz),
  .resetn(resetn),
  /* slave axis interface */
  .s_axis_tdata(i2s_rdata),
  .s_axis_tlast(1'b0),
  .s_axis_tvalid(i2s_rx_tvalid),
  .s_axis_tready(),
  /* master axis interface */
  .m_axis_tdata(i2s_rdata_bass),
  .m_axis_tlast(),
  .m_axis_tvalid(),
  .m_axis_tready(),
  /* coefficients */
  .b0(24'd224240),
  .b1(24'd229645),
  .b2(24'd234769),
  .b3(24'd239599),
  .b4(24'd244122),
  .b5(24'd248326),
  .b6(24'd252203),
  .b7(24'd255740),
  .b8(24'd258930),
  .b9(24'd261764),
  .b10(24'd264235),
  .b11(24'd266337),
  .b12(24'd268064),
  .b13(24'd269412),
  .b14(24'd270377),
  .b15(24'd270957),
  .b16(24'd271151)
  );

  assign i2s_ldata_fir_acc = freq_sw[0]? i2s_ldata: i2s_ldata_bass+i2s_ldata_mid;// + i2s_ldata; 
  assign i2s_rdata_fir_acc = freq_sw[0]? i2s_rdata: i2s_rdata_bass+i2s_rdata_mid;// + i2s_rdata; 

  assign i2s_data_tx = {i2s_ldata_fir_acc[22:0], 1'b0, 8'd0, i2s_rdata_fir_acc[22:0], 1'b0, 8'd0};

  axis_i2s_tx_v1_0 i2s_tx_inst0 (
  .aclk(clk25mhz), 
  .resetn(resetn),
  /* slave axis interface */
  .s_axis_tdata(i2s_data_tx), /* format = [l_data, 8'd0, l_data, 8'd0] */
  .s_axis_tlast(),
  .s_axis_tvalid(i2s_rx_tvalid),
  .s_axis_tready(), 
  /* i2s interface */
  .mclk(tx_mclk),
  .lrck(tx_lrck),
  .sclk(tx_sclk),
  .sdout(tx_data)
  );

endmodule
```

This Verilog code defines a module named `top_equalizer`, which serves as the top-level module for testing I2S (Inter-IC Sound) modules. Let's break down the code and its functionalities:

### Module Header:
- **Module Name**: `top_equalizer`
- **Author**: P Trujillo (pablo@controlpaths.com)
- **Date**: June 2021
- **Description**: Top module to test the I2S modules.
- **Revision**: 1.0 Module created

### Module Ports:
- **Inputs**:
  - `clk100mhz`: Clock input at 100 MHz.
  - `freq_sw[3:0]`: 4-bit input for frequency switch.
  - `rx_data`: Input for receiving I2S data.

- **Outputs**:
  - `led[3:0]`: 4-bit output for LEDs.
  - `tx_mclk`, `tx_lrck`, `tx_sclk`, `tx_data`: Outputs for I2S transmit interface.
  - `rx_mclk`, `rx_lrck`, `rx_sclk`: Outputs for I2S receive interface.

### Internal Signals:
- **Wires**:
  - `i2s_data_rx[63:0]`: 64-bit data stream received from I2S.
  - `i2s_data_tx[63:0]`: 64-bit data stream to be transmitted via I2S.
  - `i2s_ldata[23:0]`, `i2s_rdata[23:0]`: Left and right channel data extracted from received I2S data.
  - Various other wires for filtering and processing I2S data.

### Modules and Instantiations:
- **Clock Wizard**: Generates a 25 MHz clock from the 100 MHz clock.
- **I2S Receiver (`axis_i2s_rx_v1_0`)**: Receives I2S data and extracts left and right channel data.
- **FIR Filters (Low-pass and Mid-pass)**: Filters the received left and right channel data separately.
- **I2S Transmitter (`axis_i2s_tx_v1_0`)**: Transmits processed data via I2S.

### Signal Processing:
- Left and right channel data received from I2S are processed separately by FIR filters.
- FIR filters are used for bass and mid-frequency filtering.
- The processed data is combined for both channels based on the `freq_sw` input.

### Output Generation:
- Processed left and right channel data are combined to form the output data stream.
- Output data stream is structured in the format `[left_data, 8'd0, 0, right_data, 8'd0, 0]`.

### Notes:
- The `MARK_DEBUG` pragma is used to mark certain signals for debug purposes.
- The FIR filter coefficients are predefined and specified in the module instantiations.
- The module lacks the implementation of `m_axis_tlast` and `m_axis_tready` signals in several instantiations. These signals are usually used to indicate the end of a packet and the readiness to accept new data, respectively. Their absence might affect the proper functioning of the design.
- The LED assignment based on `freq_sw[0]` indicates that the first bit of `freq_sw` controls the LED output.

### Overall Functionality:
- This module serves as a top-level module for testing I2S modules, specifically focusing on audio equalization by processing received audio data through FIR filters. The filters can be switched based on the input `freq_sw`. The processed audio data is then transmitted back through the I2S interface.