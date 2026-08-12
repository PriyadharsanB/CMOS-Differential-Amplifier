# CMOS-Differential-Amplifier
Design and analysis of CMOS Differential Amp in LTSpice using TSMC's 180nm node process parameters, , built as a foundational analog IC design study — covering the differential pair, active load, and current-mirror biasing that form the front end of most op-amps.

By Priyadharsan 
---

## Overview

The amplifier consists of an NMOS differential pair, a PMOS active load (current mirror), and an NMOS current-mirror bias network, converting a differential input into a single-ended amplified output.

Design targets: **GBW ≈ 5 MHz**, **SR ≈ 10 V/µs**, VDD = 1.8 V. The tail current (~50 µA) was derived directly from the slew rate requirement (SR = I/CL, with CL = 10 pF), and W/L ratios were sized to hit sufficient gm without pushing devices out of saturation across the 0.8 V–1.6 V input common-mode range.

## Results

| Parameter | Value |
|---|---|
| DC Gain | 27.08 dB |
| GBW | 5.3 MHz |
| Cut-off Frequency | 233.8 kHz |
| CMRR | 64 dB |
| PSRR | 27 dB |
| Slew Rate | 10 V/µs |
| Phase Margin | 92.3° |
| Power Dissipation | 90 µW |

## Insights

**AC Analysis** — The amplifier exhibits a single dominant pole at ~233.8 kHz, rolling off at a clean -20 dB/decade, confirming first-order behavior. Gain stays flat at ~28 dB up to the pole and then decays linearly with frequency, giving a GBW of 5.3 MHz. Since GBW = Av × fp, the bottleneck is directly traceable to the output resistance of the active load — the dominant pole is set almost entirely by Rout and CL, meaning any improvement in bandwidth has to come from reducing CL or increasing Rout, not from pushing gm higher.

**CMRR** — CMRR came out to ~64 dB and stays essentially flat at low frequencies before rolling off at higher frequencies. Since CMRR is the ratio of differential gain to common-mode gain, a high value here reflects good symmetry between the two branches of the differential pair — the tail current source is doing its job of keeping common-mode signals from reaching the output. The roll-off at high frequency isn't from the tail source itself, but from parasitic capacitances that start providing an AC path around it, letting common-mode signals leak through more effectively as frequency increases.

**PSRR** — PSRR measured ~27 dB, noticeably lower than CMRR, and this gap is the clearest sign of where the design's weakness lies. A simple (non-cascoded) current mirror has limited output impedance, so supply ripple on VDD couples through the mirror's finite output resistance almost directly to the output node. This is consistent with the design insight that gain and PSRR share the same root cause — both are limited by how much output resistance a single-stage current mirror can offer — which is why cascoding is the natural next step rather than a separate fix.

**Channel Length Modulation** — Sweeping L from 180 nm to 500 nm in 80 nm steps pushed DC gain up toward 40 dB, while the cutoff frequency barely moved. This split is explained by where each quantity comes from: ro ≈ 1/(λ·ID), and λ scales down as L increases, so ro (and hence Av = gm·ro) rises steadily with channel length. But the cutoff frequency depends on fc ≈ gm/(2πCL) — a ratio that's essentially independent of ro — so lengthening the channel buys gain "for free" without touching bandwidth. This is exactly why gain and bandwidth are treated as separable design knobs in this topology: L controls one, CL and gm control the other.

## Repository Structure
