# Audio equalizer based on FIR filters
Link: <https://www.controlpaths.com/2021/06/28/audio-equalizer-based-on-fir-filters/>

- Digital Signal Processing
- Audio Processing: works directly with the waves, and is based on modifying the wave itself to obtain different effects.

The paragraph explains:
- How to acquire sound signals
- The protocols used
- How to design a simple equalizer to modify the amplitude of different frequency bands built with some FIR filters

![image](/pictures/fig1.svg)

Use microphone with amplifier/Electric guitar/Processor -> An audio signal (AC signal of many different frequencies).

- Use different ADCs with different numbers of bits, or different sample rates.
- Analog Devices or Texas Instruments design usually 24 bits of resolution to acquire signals at standard audio sampling rates like 44 ksps, 96 ksps or 192 ksps (selected ~ bandwidth that the human ear can acquire 20Hz to 20kHz).

![image](/pictures/fig2.png)

- Nyquist theorem: Need at least twice of frequency to acquire a signal without data loss => Lower frequency that an audio ADC can be acquired must be at least 40ksps.
- Sample rates needed to acquire audio signals are very limited, compared with a standard ADC that can acquire up to 500ksps or 1Msps. (COST)
- Same as the ADC, there are special DACs (resolution of 24 bits, and sample rates of 44ksps, 96ksps and 192ksps)

In this project, use:

![image](/pictures/fig3.png)
- This Pmod features a ***Cirrus ADC CS5343***, with 24 bits of resolution and a sample rate up to 108 ksps. To synthesize the signal, this PMOD also includes a ***DAC Cirrus CS4344***, a DAC with 24 bits of resolution, and sampling rate of up to 200 ksps.

![image](/pictures/fig4.png)
- The protocol used to read the acquired signal by the ADC, and also to write the corresponding signal to DAC, is ***Inter IC Sound (I2S)***.
- I2S is a protocol designed to transfer digital sound through different devices.

The protocol is based on 4 lines
- One for the ***system clock (MCLK)***, which is used for the delta-sigma modulator/demodulator
- Another clock for the ***communication (SCLK)***
- A third clock that is corresponding with the sample rate ***(LRCK)***. This clock is a prescaled clock from SCLK.
- Finally the ***data signal (DATA)***.

I2S is a protocol that is designed for ***stereo signals***, which that means in any sample, a signal for right and left speakers has to be sent. For this reason, the data signal is a ***double data rate (DDR)*** signal with the LRCK signal.

## [Addition] I2S — Inter-IC sound interface
Link: <https://infocenter.nordicsemi.com/index.jsp?topic=%2Fcom.nordic.infocenter.nrf52832.ps.v1.1%2Fi2s.html>

The I2S (Inter-IC Sound) module, supports the original two-channel I2S format, and left or right-aligned formats. It implements EasyDMA for sample transfer directly to and from RAM without CPU intervention. The I2S peripheral has the following main features:
- Master and Slave mode
- Simultaneous bi-directional (TX and RX) audio streaming
- Original I2S and left- or right-aligned format
- 8, 16 and 24-bit sample width
- Low-jitter Master Clock generator
- Various sample rates

![image](/pictures/fig7.png)

### Mode
- The I2S protocol specification defines two modes of operation, Master and Slave.
- The I2S mode decides which of the two sides (Master or Slave) shall provide the clock signals LRCK and SCK, and these signals are always supplied by the Master to the Slave.

### Transmitting and receiving
- The I2S module supports both transmission (TX) and reception (RX) of serial data. In both cases the serial data is shifted synchronously to the clock signals SCK and LRCK.
- TX data is written to the SDOUT pin on the falling edge of SCK, and RX data is read from the SDIN pin on the rising edge of SCK. The most significant bit (MSB) is always transmitted first.
- TX and RX are available in both Master and Slave modes and can be enabled/disabled independently in the CONFIG.TXEN and CONFIG.RXEN.
- Transmission and/or reception is started by triggering the START task. When started and transmission is enabled (in CONFIG.TXEN), the TXPTRUPD event will be generated for every RXTXD.MAXCNT number of transmitted data words (containing one or more samples). Similarly, when started and reception is enabled (in CONFIG.RXEN), the RXPTRUPD event will be generated for every RXTXD.MAXCNT received data words.

![image](/pictures/fig5.png)

### Left right clock (LRCK)
- Often referred to as "word clock", "sample clock" or "word select" in I2S context, is the clock defining the frames in the serial bit streams sent and received on SDOUT and SDIN, respectively.
- In I2S mode, each frame contains one left and right sample pair, with the left sample being transferred during the low half period of LRCK followed by the right sample being transferred during the high period of LRCK.
- In Aligned mode, each frame contains one left and right sample pair, with the left sample being transferred during the high half period of LRCK followed by the right sample being transferred during the low period of LRCK.
- Consequently, the LRCK frequency is equivalent to the audio sample rate.
- When operating in Master mode, the LRCK is generated from the MCK, and the frequency of LRCK is then given as:
`LRCK = MCK / CONFIG.RATIO`
- LRCK always toggles around the falling edge of the serial clock SCK.

### Serial clock (SCK)
- The serial clock (SCK), often referred to as the serial bit clock, pulses once for each data bit being transferred on the serial data lines SDIN and SDOUT.
- When operating in Master mode the SCK is generated from the MCK, and the frequency of SCK is then given as:
`SCK = 2 * LRCK * CONFIG.SWIDTH`
- The falling edge of the SCK falls on the toggling edge of LRCK.
- When operating in Slave mode SCK is provided by the external I2S master.

### Master clock (MCK)
- The master clock (MCK) is the clock from which LRCK and SCK are derived when operating in Master mode.
- The MCK is generated by an internal MCK generator. This generator always needs to be enabled when in Master mode, but the generator can also be enabled when in Slave mode. Enabling the generator when in slave mode can be useful in the case where the external Master is not able to generate its own master clock.
- The MCK generator is enabled/disabled in the register CONFIG.MCKEN, and the generator is started or stopped by the START or STOP tasks.
- In Master mode the LRCK and the SCK frequencies are closely related, as both are derived from MCK and set indirectly through CONFIG.RATIO and CONFIG.SWIDTH.
- When configuring these registers, the user is responsible for fulfilling the following requirements:
- SCK frequency can never exceed the MCK frequency, which can be formulated as: `CONFIG.RATIO >= 2 * CONFIG.SWIDTH`
- The MCK/LRCK ratio shall be a multiple of 2 * CONFIG.SWIDTH, which can be formulated as: `Integer = (CONFIG.RATIO / (2 * CONFIG.SWIDTH))`
- The MCK signal can be routed to an output pin (specified in PSEL.MCK) to supply external I2S devices that require the MCK to be supplied from the outside.
- When operating in Slave mode, the I2S module does not use the MCK and the MCK generator does not need to be enabled.

![image](/pictures/fig6.png)

## [Main]
- For this project, I have designed 2 different modules: One to transmit data through I2S & The other to read data through I2S. 
- In both cases, the module shares all the clocks and prescaler logic, with the only changes in the read and write data line. 
- Both modules work with AXI4-Stream protocol with a data width of 64 bits (dataL + 8 bit padding + dataR + 8 bit padding). 
- With this configuration, the relation between SCLK and LRCK is 64. 
- The module is designed for a 25MHz clock and is ready to run at 3 different speeds, 48kHz 97kHz and 194kHz, according to the width of the prescaler.
- Notice that the speed of 194 is out of the range of the ADC, so this mode has to be disabled in the ADC.

![image](/pictures/fig8.png)

```verilog
assign lrck = pulse_counter[7]; /* change to 8 for 44kHz, 7 for 96kHz, 6 for 192kHz */
assign mclk = aclk;
assign sclk = pulse_counter[1]; /* fixed to 64 counts per lrck cycle. Change to 2 for 44kHz, 1 for 96kHz, 0 for 192kHz */
```

Next you can see the result of the simulation of the I2S module.

![image](/pictures/fig9.png)

With these 2 modules, we can design a simple bridge to verify the behavior of both modules. The script to generate this block design is available on Github.

![image](/pictures/fig10.png)

When you implement this design, you will notice that the volume when the audio signal passes through the bridge has decreased. This happens because of a divisor that you can find in the PMOD I2S2, in the ADC input. In order to compensate this attenuation, you can shift the left and the right signals one position to the left to add a gain of 2 to the bridge.

![image](/pictures/fig11.png)

- At this point we have a way to acquire and synthesize the audio signals, now is the turn of the processing. 
- In this post, we are going to develop a 3-band equalizer.
- An equalizer is a device that can apply different gains according to the frequency of the signal, for example, if we need out audio signal travels a long distance, we will have no problem with low-frequency signals since they can travel long distances easily, but high-frequency signals suffer a high attenuation with the distance, so we will need to amplify high frequencies, and maybe attenuate low frequencies. 
- Another example could be the use of different speakers, and the equalizer has to send to each speaker the frequencies that can reproduce, low frequencies for the woofer and high frequencies for the tweeters. 
- In any of these examples, we need to split the signals according to its frequencies, and then amplify or attenuate each group of frequencies according the use of the signal. 
- The equalizer we will design will have 3 different bands, the Bass band, for frequencies between 1Hz and 800Hz, the Medium band for frequencies between 800Hz and 3000Hz, and the Treble band for frequencies between 3000Hz and 8000Hz. We could add a fourth band from 8000Hz to 20000Hz, but in general, frequencies above 5kHz have small amplitudes and only the harmonics of some instruments can achieve this frequency, so for this example, we only equalize up to 8000kHz.
- Now, in order to split the audio signals in these 3 different bands, we have some options, perform a DFT and then delete all the components we don’t need and finally perform an inverse DFT to synthesize again the signal, or use filters like FIR or IIR.
- The first option is not optimal for real-time audio processing, since the time spent in the DFT computing will be translated into a pause in the output signal, which is not desirable. 
- To perform real-time audio processing the best option are filters. We know 2 different kinds of digital filters that can help us to split the signal, FIR filters and IIR filters.
- The advantages of using IIR filters are many, starting for the high attenuation that we can achieve with low-order filters. Also, if we need narrow bands, these filters are also the best choice, but this time there is another player in the field, the group delay.
- Mathematically, group delay can be defined as the derivative of the spectral phase of the filter, practically, group delay is the delay in samples for each frequency.
- Imagine you have a copper wire with a length of L. If we connect one side of the wire to a function generator and apply to the wire a signal of 100Hz, the signal spent a T second to arrive on the other side of the wire. In case we apply a frequency of 1kHz, the signal will spend the same amount of time, T seconds. We can say that the wire has a group delay of T seconds, in other words, has a constant group delay so is a linear phase system. 
- If instead of a wire, we have an IIR filter and we apply the same signals, we will see how the amount of time that the 100hz signal spends to arrive on the other side is different than the 1kHz signal. This will cause in some moments, the input frequency components will be different than the output components. 
- In the case of audio signals, where the signal in a sum of different frequencies, this could be a problem, for example, if we are processing a guitar chord, where different frequencies will produce a particular sound, this sound will be different in the output of our algorithm. This effect will provoke a distortion in the audio signal. 
- So, can not we use IIR filters in audio systems? Obviously, there are solutions for almost all problems, but for now, we will change the way we will split our audio signal.
- As we said before, and you sure know, there are other kinds of digital filters, the FIR filters. This filter has, like the IIR filters, advantages and disadvantages. 
- As disadvantage, we will need a very high order filter to achieve the attenuation we will need to design a narrow band equalizer. Also, high-order filters, cause a high amount of operations must be performed, and this is translated in time. 
- On the other side, they are very easy to design, they are always stables and, they have a linear phase, in some cases.

![image](/pictures/fig12.png)

- The cases where the FIR filters have linear phase are where the coefficients are symmetric, and luckily, this condition is not hard to accomplish. 
- In fact, respective FIR design functions of MATLAB or Python design by default symmetric FIR filters, so this means that, with its disadvantages, we can design an audio equalizer with FIR filters without generating a distortion in the signal. For symmetric FIR filters, the group delay is N/2 samples.
- Once we have decided which kind of filters we will use, we have to design the filters with any tool. In my case I used Python. 
- You will notice that the different bands have a little gap between them. These gaps will cause signal attenuation in those bands, but they can be corrected by applying a gain on the corresponding band. Also, if we allow a high cross-band between filters, will be hard to amplify, for example only the bass band, or the mid-band.

![image](/pictures/fig13.svg)

- Regarding the design of the filters, they are designed with 33 taps, and for the bass and mid, I have used a square window to improve the attenuation. This will cause a ripple in the mid-band. For the treble band, I used a Hanning window.
- Regarding the HDL design of the filter, I have designed a new FIR filter that takes advantage of the filter symmetry to perform half the operations. The coefficients of a symmetric FIR filter accomplish the next rule: $$b_i=b_{N−i}$$
- Using this, we can perform half of the multiplications if we add previously the corresponding samples. An example of 4th order FIR filter is shown in the next figure.

![image](/pictures/fig14.png)

- The filter designed has 16 coefficient inputs to implement a 32nd order FIR filter. The previous addition adds the 2 corresponding samples, except when the index is equal to 16 since only one sample is added.

```verilog
/* macc operation */
assign input_bn_temp = (index == 16)? input_pipe[index]: (input_pipe[index]+input_pipe[32-index]);
assign input_bn = input_bn_temp * bx_int[index];
```

The top module of the equalizer will be built by the I2S transmitter and receiver, and 3 pairs of filters for the right and the left channels. A gain will be applied to the output of the filters in order to manually equalize the output.

![image](/pictures/fig15.png)

- Audio signals are a very good option if you are starting with signal processing for some reasons. 
- First, you don’t need a signal generator since any smartphone, MP3 player or computer can generate an audio signal, also, both MATLAB and Python have packages to read and process .wav files, so you can check your DSP algorithm. 
- Also, you can use your microphone input to verify the behavior, and of course, you can also verify your algorithm and experiment with it by simply listening to the output. 
- I spent the past 2 weekends exploring different kinds of equalizations and has been very interesting.
- You will notice that the equalizer we have designed here is very simple, and in most cases, we will need more performance in terms of filtering. I will explain this with an example. 
- The next image is a collection of the frequency spectrum of a song. The image is obtained by dividing a song into different windows and then performing the DFT of each window.

![image](/pictures/fig16.svg)

- We can see how the major part of the harmonics are located below 1kHz, and the harmonics above 3khz are very small. That means, we will need narrow-band filters to split the frequencies, and this is hard to get with FIR filters. In the next posts, we will see how we can equalize IIR filters to obtain a linear phase, at least in the pass band. Keep connected!

## [Addition] Introduction to FIR Filters
Link: <https://www.just.edu.jo/~hazem-ot/Lab.4&5.%20FIR%20Filters.pdf>

- In signal processing, a finite impulse response (FIR) filter is a filter whose impulse response (or response to any finite length input) is of finite duration, because it settles to zero in finite time. 
- This is in contrast to infinite impulse response (IIR) filters, which may have internal feedback and may continue to respond indefinitely (usually decaying). 
- The impulse response of an Nth-order discrete-time FIR filter (i.e., with a Kronecker delta impulse input) lasts for N + 1 samples, and then settles to zero. 
- FIR filters can be discrete-time or continuous-time, and digital or analog.

![image](/pictures/fig17.png)

- A discrete-time FIR filter of order $N$. 
- The top part is an $N$-stage delay line with $N + 1$ taps. 
- Each unit delay is a $z^{−1}$ operator in the Z-transform notation. 
- The output $y$ of a linear time invariant (LTI) system is determined by convolving its input signal $x$ with its impulse response $b$. 
- For a discrete-time FIR filter, the output is a weighted sum of the current and a finite number of previous values of the input. 
- The operation is described by the following equation, which defines the output sequence $y[n]$ in terms of its input sequence $x[n]$:

![image](/pictures/fig18.png)

- $x[n]$ is input signal, $y[n]$ is output signal
- ![image](/pictures/fig19.png)

# Verilog-8-Band-Audio-Equalizer
Link Github: <https://github.com/Qyt0109/Verilog-8-Band-Audio-Equalizer>

# Github Code for "Audio equalizer based on FIR filters"
Link Github: <https://github.com/controlpaths/blog/tree/87fbe923b3a888f79df401bee3a0fb8242fb5258/equalizer_fir>

## Python (Use Google Colab)
...

## top_equalizer.v
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