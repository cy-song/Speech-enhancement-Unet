
# Evaluation Matrix & Failure Analysis

## Experimental Axes
- Noise domain (in / out)
- Noise count (single / multi)
- SNR (10 → -10 dB)

## Evaluation Protocol
- Fixed model weights
- Fixed STFT parameters
- Fixed random seed

## Evaluation Matrix
[table]

## Observed Failure Patterns
- Low SNR + multi-noise → word dropping
- Out-of-domain noise → residual tonal artifacts



### Evaluation Matrix (fixed model weights)

Scoring (listening): 0=none, 1=mild, 2=moderate, 3=severe

| CleanID | Domain | NoiseSetting | NoiseKeys | SNR(dB) | PESQ (N→E, Δ) | STOI (N→E, Δ) | NR(dB) | DropWords(0-3) | Residual(0-3) | Distortion(0-3) | Failure note (1 sentence) | Hypothesis (1 sentence) |
|---|---|---|---|---:|---|---|---:|---:|---:|---:|---|---|
| LS-01 | in  | single | dog(3) | 10  |  |  |  |  |  |  |  |  |
| LS-01 | in  | multi  | dog(3), NRtest_key | 10 |  |  |  |  |  |  |  |  |
| LS-01 | out | single | fan | 10 |  |  |  |  |  |  |  |  |
| LS-01 | out | multi  | fan, cafe | 10 |  |  |  |  |  |  |  |  |
| ME-01 | in  | single | dog | 10 |-0.02  |-0.01  |0.86  |0.3  |2  |0.5  |At SNR = 10, the enhanced signal largely preserves speech intelligibility with minimal phoneme loss, but exhibits increased residual reverberation-like artifacts.  | Likely due to conservative suppression at high SNR, leaving low-energy noise unreduced. |
| ME-01 | out | multi  | fan, cafe | 10 |  |  |  |  |  |  |  |  |


## Evaluation Results Table 2

| CleanID | Domain | NoiseSetting | NoiseKeys | SNR(dB) | PESQ (N→E, Δ) | STOI (N→E, Δ) | NR(dB) | DropWords(0-3) | Residual(0-3) | Distortion(0-3) | Failure note (1 sentence) | Hypothesis (1 sentence) |
|---|---|---|---|---:|---|---|---:|---:|---:|---:|---|---|
| LS-01 | in  | single | dog | 10  | -0.82 |-0.15  |-3.15  |1  |2  | 2 |Noise energy reduced but error increases  | Error-based metric sensitive to subtle speech modifications|
| LS-01 | in  | single | dog | 5   | -0.6 | -0.17 |0.57  |0  |3  |1(掩蓋了一點)  | Narrow-band reverse-beep dominates noise segment  |  Mask-based UNet preserves stable tonal components overlapping speech |
| LS-01 | in  | single | dog | 0   | -0.59 |-0.14  |5.59  | 3 | 2 | 2 | Word drop with residual high-frequency noise |  Mask suppresses speech-dominant bands while leaving high-frequency residuals |
| LS-01 | in  | single | dog | -5  | -0.09 | -0.06 | +9.80 dB | 1 |3  |2  | Speech attenuation + strong residual noise |Over-suppression lowers speech; nonstationary noise not removed  |
| LS-01 | in  | single | dog | -10 | -0.04  | -0.07 | +11.31 dB | 3 |3  | 1 | Severe word drop with strong residual noise |Over-suppression removes speech while nonstationary noise remains at very low SNR  |
| LS-01 | in  | multi  | dog, nrtest_key | 10  | -0.12 | -0.12 |-3.28dB  | 0 |2  | 1 |  Residual noise prominent under multi-noise mix  |Mask struggles to suppress diverse noises and leaves residual components at mild SNR  |
| LS-01 | in  | multi  | dog, nrtest_key | 5   | -0.18 | -0.11 | +1.30 | 0 | 3 | 1 （後半段音節被壓小、屬輕度動態/能量變形）| Residual noise remains prominent, and several late syllables are attenuated without clear word drops. |Mask over-suppresses weaker speech while leaving nonstationary noise at mid SNR  |
| LS-01 | in  | multi  | dog, nrtest_key | 0   | -0.02 | -0.06 | +5.06 dB | 2 |2（仍有殘噪，但狗叫與鍵盤聲明顯變小）  |2（其中一字屬音量衰減型掉字）  |Dog bark reduced but two words drop with residual hiss.  | Mask removes structured transient sounds while leaving unstructured broadband residuals and over-suppressing speech at low SNR |
| LS-01 | in  | multi  | dog, nrtest_key | -5  | -0.05 | -0.11 | +8.98 dB | 0 |3 （狗叫高頻殘留明顯） |2（兩個字音節明顯被壓小）　 | No word drops, but two syllables attenuated with strong high-frequency dog-bark residuals. |Mask weakens low-energy speech while failing to suppress high-frequency components at very low SNR.  |
| LS-01 | in  | multi  | dog, nrtest_key | -10 | -0.02 | +0.05 | +12.80 dB |0  | 3（殘餘高頻噪音明顯） | 3（4 個音節近乎聽不清、屬強烈衰減） |No word drops, but four syllables become nearly inaudible with strong high-frequency residual noise.  | Severe over-suppression collapses weak speech regions while leaving high-frequency residuals at extreme low SNR. |
| LS-01 | out | single | game | 10  |  |  |  |  |  |  |  |  |
| LS-01 | out | single | game | 5   |  |  |  |  |  |  |  |  |
| LS-01 | out | single | game | 0   |  |  |  |  |  |  |  |  |
| LS-01 | out | single | game | -5  |  |  |  |  |  |  |  |  |
| LS-01 | out | single | game | -10 |  |  |  |  |  |  |  |  |
| LS-01 | out | multi  | game, cafe | 10  |  |  |  |  |  |  |  |  |
| LS-01 | out | multi  | game, cafe | 5   |  |  |  |  |  |  |  |  |
| LS-01 | out | multi  | game, cafe | 0   |  |  |  |  |  |  |  |  |
| LS-01 | out | multi  | game, cafe | -5  |  |  |  |  |  |  |  |  |
| LS-01 | out | multi  | game, cafe | -10 |  |  |  |  |  |  |  |  |
| ME-01 | in  | single | dog | 10  |  |  |  |  |  |  |  |  |
| ME-01 | in  | single | dog | 5   |  |  |  |  |  |  |  |  |
| ME-01 | in  | single | dog | 0   |  |  |  |  |  |  |  |  |
| ME-01 | in  | single | dog | -5  |  |  |  |  |  |  |  |  |
| ME-01 | in  | single | dog | -10 |  |  |  |  |  |  |  |  |
| ME-01 | out | multi  | game, cafe | 10  |  |  |  |  |  |  |  |  |
| ME-01 | out | multi  | game, cafe | 5   |  |  |  |  |  |  |  |  |
| ME-01 | out | multi  | game, cafe | 0   |  |  |  |  |  |  |  |  |
| ME-01 | out | multi  | game, cafe | -5  |  |  |  |  |  |  |  |  |
| ME-01 | out | multi  | game, cafe | -10 |  |  |  |  |  |  |  |  |

## Evaluation Order (How to Fill the Table)

Each **row block** is defined by a fixed tuple:
**(CleanID, Domain, NoiseSetting, NoiseKeys)**.
For each fixed tuple, run a single inference sweep over:
**SNR = [10, 5, 0, -5, -10]**, then fill the 5 rows.

Fill order:
1. Fix **CleanID = LS-01**.
2. Fill **in-domain** first:
   - single (NoiseKeys fixed) → sweep SNR 10/5/0/-5/-10 → fill 5 rows
   - multi  (NoiseKeys fixed) → sweep SNR 10/5/0/-5/-10 → fill 5 rows
3. Then fill **out-domain**:
   - single → sweep SNR → fill 5 rows
   - multi  → sweep SNR → fill 5 rows
4. Only after LS-01 is complete, switch to **CleanID = ME-01** and repeat the same order.

Rule: only change **one axis at a time** (NoiseSetting or Domain or CleanID), keep the others fixed.


