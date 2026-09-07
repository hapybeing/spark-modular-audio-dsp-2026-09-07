# AETHERIS // Modular Web Audio DSP Synthesizer & Spectral FFT Engine


[![License: MIT](https://img.shields.io/badge/License-MIT-emerald.svg)](https://opensource.org/licenses/MIT)
[![Tech Stack](https://img.shields.io/badge/Stack-Web%20Audio%20API%20%7C%20Tailwind%20%7C%20ES6%2B%20%7C%20HTML5%20Canvas-cyan.svg)](#architecture)

> **A production-grade, zero-dependency Modular Web Audio DSP Synthesizer and Spectral Acoustic Visualizer with 2048-bin FFT resolution, 3D waterfall spectrogram, dual-channel phosphor oscilloscope, stereo Lissajous phase vector scope, polyrhythmic step sequencer, and hardware-inspired analog drift modeling.**

### 🌐 Live Production Deployment
**Experience the live interactive synthesizer here:**  
[https://hapybeing.github.io/spark-modular-audio-dsp-2026-09-07/](https://hapybeing.github.io/spark-modular-audio-dsp-2026-09-07/)

---

## 🔬 Mathematical & Algorithmic Foundations

### 1. Dual-Operator Frequency Modulation (FM) Matrix
The FM matrix implements Chowning frequency modulation synthesis, where Carrier Oscillator $\text{Osc}_1$ has its instantaneous angular frequency modulated by Modulator Oscillator $\text{Osc}_2$:
$$y(t) = A_1(t) \cdot \sin\left(\omega_1 t + \frac{I(t)}{\omega_2} \cdot \sin(\omega_2 t)\right)$$
Where:
- $\omega_1 = 2\pi f_1$ (Carrier angular velocity)
- $\omega_2 = 2\pi f_2$ (Modulator angular velocity)
- $I(t)$ is the dynamic modulation index determined by the modulation depth envelope and modulation gain.

### 2. Fast Fourier Transform & Spectral Centroid Computation
The real-time telemetry engine continuously analyzes the complex frequency spectrum using a 2048-point Fast Fourier Transform (FFT). The Spectral Centroid (brightness metric representing the spectral center of mass in Hertz) is computed as:
$$\text{Centroid} = \frac{\sum_{k=0}^{N/2} f_k \cdot |X(k)|}{\sum_{k=0}^{N/2} |X(k)|}$$
Where $f_k = \frac{k \cdot f_s}{N}$ represents the center frequency of bin $k$, and $|X(k)|$ is the spectral magnitude.

### 3. Root-Mean-Square (RMS) & Peak Telemetry (dBFS)
Continuous signal loudness and amplitude headroom are calculated in decibels relative to full scale:
$$\text{RMS}_{\text{dBFS}} = 20 \log_{10} \left( \sqrt{\frac{1}{N} \sum_{n=0}^{N-1} x[n]^2} \right)$$
$$\text{Peak}_{\text{dBFS}} = 20 \log_{10} \left( \max_{n} |x[n]| \right)$$

### 4. Non-Linear Soft-Saturation Waveshaper
The overdrive circuit executes a polynomial hyperbolic tangent saturation curve across an oversampled 44.1 kHz grid ($k \in [0, 50]$):
$$f(x) = \frac{(3 + k) \cdot x \cdot 20^\circ}{\pi + k \cdot |x|}$$
This produces odd and even harmonic distortion clusters that enrich saw and pulse waves with warm tube characteristics without harsh digital aliasing.

### 5. Algorithmic Convolution Reverb (Procedural Impulse Response)
Instead of loading static external audio files, AETHERIS procedurally synthesizes a stereo diffuse impulse response $h_L(t), h_R(t)$ on initialization:
$$h(t) = w(t) \cdot e^{-\alpha t}$$
Where $w(t) \sim \mathcal{U}(-1, 1)$ is uncorrelated white noise and $\alpha$ governs room absorption decay.

### 6. Zero-Crossing Trigger Oscilloscope Stabilization
To prevent waveform jitter on the live time-domain canvas, an autocorrelation zero-crossing threshold detector searches for $x[i] < 0 \land x[i+1] \ge 0$ to anchor the phase position of the primary oscillation.

---

## 🎛️ Feature & Module Architecture

| Module | Core Functionality |
| :--- | :--- |
| **01 // Oscillators & FM** | Dual multi-waveform oscillators (Saw, Square, Triangle, Sine), sub-oscillator (-1 octave), pink/white noise generator, octave/semitone/fine detune adjustments, and FM cross-modulation matrix. |
| **02 // Filter & LFO** | 4-pole simulated multi-mode biquad filter (Lowpass, Highpass, Bandpass, Notch) with logarithmic frequency sweep (20 Hz - 18 kHz), resonance $Q \in [0.1, 24]$, and routable multi-wave LFO (Filter, Pitch, Amp, Pan). |
| **03 // Dual Envelopes** | Independent Attack-Decay-Sustain-Release (ADSR) generators for Amplifier and Filter with dynamic real-time SVG curve visualizations. |
| **04 // Stereo FX Rack** | Tube saturation/overdrive, cross-feedback stereo ping-pong delay with dampening, algorithmic convolution reverb, and stereo quadrature chorus. |
| **Sequencer & Quantizer** | 16-step polyrhythmic sequencer with lookahead clock scheduling, swing feel, step velocity bars, octave shifts, and scale quantization (Minor Pentatonic, Dorian, Phrygian, Blues, etc.). |
| **Visualizer Suite** | 4 selectable 60 FPS GPU-accelerated modes: Logarithmic FFT Spectrum, Stabilized Phosphor Oscilloscope, Goniometer Lissajous Phase Vector Scope, and 3D Waterfall Spectrogram. |

---

## 🎹 Interaction & Keyboard Guide

### Hardware & Virtual Controls
- **Virtual Keyboard**: 2-Octave piano interface with multi-touch and mouse drag support.
- **Computer Keyboard Mapping**:
  - Lower Octave: `Z` (C), `S` (C#), `X` (D), `D` (D#), `C` (E), `V` (F), `G` (F#), `B` (G), `H` (G#), `N` (A), `J` (A#), `M` (B)
  - Upper Octave: `Q` (C), `2` (C#), `W` (D), `3` (D#), `E` (E), `R` (F), `5` (F#), `T` (G), `6` (G#), `Y` (A), `7` (A#), `U` (B)
- **Global Shortcuts**:
  - `[SPACE]`: Toggle Step Sequencer playback
  - `[P]`: Panic trigger (immediate voice silence)
  - `[R]`: Toggle direct master audio recording (downloads WebM/WAV)

---

## 🚀 Sound Presets Included

1. **Cyberpunk Saw Lead**: High-energy dual detuned saws with resonant filter envelope punch and stereo ping-pong echoes.
2. **Glassy Ethereal FM**: Crystalline bell timbre with high harmonic frequency modulation index, long decay, and lush algorithmic reverb.
3. **Acid 303 Resonant Pluck**: High-resonance screaming squelch with fast filter envelope, overdrive saturation, and rhythmic delay.
4. **Cosmic Void Drone**: Massive slow-evolving ambient soundscape featuring detuned supersaws, pink noise, and deep space reverb.
5. **Retrowave Poly Brass**: Warm 1980s synth brass with gentle envelope swell and dimensional stereo chorus.

---

## 🏗️ Architecture & Deployment

- **Single Production-Grade Bundle**: Everything packaged into a self-contained, optimized `index.html`.
- **Zero Heavy Audio Frameworks**: 100% native Web Audio API graph utilizing hardware audio worklets and nodes.
- **Styling**: Tailwind CSS via modern CDN with custom obsidian glassmorphic design tokens.
- **Continuous Deployment**: Automated GitHub Actions workflow pushing directly to GitHub Pages on commit to `main`.

---

## 📄 License
Open source under the [MIT License](LICENSE).
