
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
| LS-01 | out | single | car | 10  | -0.26 | -0.2 |-4.6dB  |0 (音節稍不自然) |2(仍有空間感底噪)  |2  |No word drops, but mild syllable distortion appears with diffuse broadband residual noise after traffic suppression. | Structured traffic noise is suppressed, while diffuse background and speech harmonic smearing remain. |
| LS-01 | out | single | car | 5   |-0.09  |-0.22  |-1.19 dB  | 0 | 2 | 2| No word drops, but many syllables are attenuated with similar diffuse broadband residuals (roomy noise) as at 10 dB. | Out-of-domain traffic causes over-suppression of weak speech regions while diffuse background components remain, driving a large STOI drop despite modest NR. |
| LS-01 | out | single | car | 0   | -0.03 | -0.22 | +2.22 dB | 3 |3 | 2 |Multiple word drops with strong diffuse residual noise, requiring listening effort to follow the content.  | At 0 dB out-of-domain traffic, the mask over-suppresses weak speech regions while failing to remove diffuse background noise, leading to word loss and intelligibility collapse. |
| LS-01 | out | single | car | -5  | +0.01 |-0.18  |+6.02 dB  | 3 |3  | 2 | Severe word drops with strong diffuse residual noise; speech becomes largely unintelligible at -5 dB. | Aggressive masking reduces noise energy (raising NR) but collapses weak speech regions and leaves diffuse background residuals, so objective scores can stay moderate while intelligibility fails. |
| LS-01 | out | single | car | -10 | +0.01 | -0.14 | +9.35 dB | 3 | 3 | 3| Severe speech collapse (especially in the second half) with strong diffuse residual noise at -10 dB. | At extreme out-of-domain SNR, the mask over-suppresses most speech energy while leaving broadband/diffuse residuals, yielding high NR but near-zero perceptual intelligibility. |
| ME-01 | in | multi  | dog, nrtest_key | 10  | +0.16 | -0.02 |+1.53 dB  |0  | 3 | 0.5 | No word drops are observed, but noise reduction is weak at 10 dB, leaving strong residual background noise while speech remains largely intact. |At high in-domain SNR, the mask operates conservatively, preserving speech energy but failing to suppress low-level background noise.  |
| ME-01 | in | multi  | dog, nrtest_key | 5   | +0.11 |-0.03  | +3.99 dB |2|3|1 |Two words are dropped at 5 dB, with strong residual noise remaining despite moderate noise reduction.  | At mid SNR, the mask begins to over-suppress weaker speech components while still failing to remove diffuse background noise. |
| ME-01 | in | multi  | dog, nrtest_key | 0   | +0.13 |+0.03  |+9.96 dB  | 0 | 3 | 2 | No word drops are observed at 0 dB, but two syllables are amplified together with noise, resulting in stronger residual noise after enhancement. |At 0 dB, the mask fails to separate speech and noise-dominant regions, amplifying overlapping components and increasing residual noise despite higher NR.  |
| ME-01 | in | multi  | dog, nrtest_key | -5  | +0.02 | +0.06 | +11.68 dB | 2 |3  |1  |One to two words are dropped at −5 dB, with strong residual noise remaining while most speech content remains perceptually intelligible.  |At low SNR, aggressive masking improves overall NR but begins to remove weak speech components, causing word drops while leaving dominant residual noise.  |
| ME-01 | in | multi  | dog, nrtest_key | -10 | +0.02 |+0.05  |+13.56 dB  | 3| 3 |1  |More than three words are dropped at −10 dB, with strong residual noise remaining while the overall speech signal still sounds structurally intact.  | At very low SNR, the mask strongly suppresses speech-dominant regions to achieve high NR, leading to significant word loss while residual noise remains prominent. |

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


