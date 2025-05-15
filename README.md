### Authors
- Gal Ben Ami
- Aharon Basous

# PMR446 Multichannel Receiver

This repository contains a single GNU Radio Companion (`.grc`) flowgraph for receiving and demodulating up to eight PMR446 voice channels simultaneously.  

---

## 📋 Overview

- **Goal**: Capture a 2 MHz slice of the PMR446 band, isolate each 12.5 kHz channel, demodulate narrowband FM (NBFM) audio, and mix all channels into one output stream.  
- **Hardware**: Any RTL-SDR-compatible device (e.g. RTL-SDR dongle, HackRF) tuned to 446.200 MHz.  
- **Software**: GNU Radio Companion (tested with version 3.9+).

---

## ⚙️ Flowgraph Process

1. **SDR Source**  
   - Captures complex IQ samples at 2 048 kS/s, centered on 446.200 MHz.

2. **Per-Channel Branches (×8)**  
   For each PMR446 channel (446.00625 MHz → 446.09375 MHz):  
   - **Frequency Xlating FIR Filter**  
     • Shifts the desired channel to baseband  
     • Applies a band-pass FIR to reject out-of-band signals  
   - **Low-Pass Filter + Decimation (×10)**  
     • Restricts audio bandwidth to ±5 kHz  
     • Drops samples to reduce rate (≈120 kS/s)  
   - **Rational Resampler (↑4)**  
     • Upsamples to 480 kS/s for the FM demodulator  
   - **Power Squelch**  
     • Mutes channel when no carrier is present  
   - **NBFM Receive**  
     • Demodulates narrowband FM to 48 kS/s audio  
     • Applies 75 µs de-emphasis (PMR446 standard)  
   - **Multiply Const**  
     • Simple software “volume” control

3. **Audio Mixer**  
   - Sums all eight demodulated audio streams  
   - Feeds into both a live **Audio Sink** (48 kHz) and a **WAV File Sink**

4. **Visualization (optional)**  
   - A QT GUI waterfall for real-time spectral monitoring

---

