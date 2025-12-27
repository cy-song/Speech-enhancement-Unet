
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
| LS-01 | in  | single | dog | 10  | -0.82 |-0.15  |-3.15  |1  |2  | 2 | SNR = 10 dB, the model reduces overall noise energy but introduces waveform-level distortions, resulting in increased error relative to the clean signal despite perceptually lower background noise.  | The error-based NR degradation is likely caused by over-modification of speech spectral components, where aggressive masking alters low-energy speech details more than it removes residual noise. |
| LS-01 | in  | single | dog | 5   |  |  |  |  |  |  |  |  |
| LS-01 | in  | single | dog | 0   |  |  |  |  |  |  |  |  |
| LS-01 | in  | single | dog | -5  |  |  |  |  |  |  |  |  |
| LS-01 | in  | single | dog | -10 |  |  |  |  |  |  |  |  |
| LS-01 | in  | multi  | dog, nrtest_key | 10  |  |  |  |  |  |  |  |  |
| LS-01 | in  | multi  | dog, nrtest_key | 5   |  |  |  |  |  |  |  |  |
| LS-01 | in  | multi  | dog, nrtest_key | 0   |  |  |  |  |  |  |  |  |
| LS-01 | in  | multi  | dog, nrtest_key | -5  |  |  |  |  |  |  |  |  |
| LS-01 | in  | multi  | dog, nrtest_key | -10 |  |  |  |  |  |  |  |  |
| LS-01 | out | single | fan | 10  |  |  |  |  |  |  |  |  |
| LS-01 | out | single | fan | 5   |  |  |  |  |  |  |  |  |
| LS-01 | out | single | fan | 0   |  |  |  |  |  |  |  |  |
| LS-01 | out | single | fan | -5  |  |  |  |  |  |  |  |  |
| LS-01 | out | single | fan | -10 |  |  |  |  |  |  |  |  |
| LS-01 | out | multi  | fan, cafe | 10  |  |  |  |  |  |  |  |  |
| LS-01 | out | multi  | fan, cafe | 5   |  |  |  |  |  |  |  |  |
| LS-01 | out | multi  | fan, cafe | 0   |  |  |  |  |  |  |  |  |
| LS-01 | out | multi  | fan, cafe | -5  |  |  |  |  |  |  |  |  |
| LS-01 | out | multi  | fan, cafe | -10 |  |  |  |  |  |  |  |  |
| ME-01 | in  | single | dog | 10  |  |  |  |  |  |  |  |  |
| ME-01 | in  | single | dog | 5   |  |  |  |  |  |  |  |  |
| ME-01 | in  | single | dog | 0   |  |  |  |  |  |  |  |  |
| ME-01 | in  | single | dog | -5  |  |  |  |  |  |  |  |  |
| ME-01 | in  | single | dog | -10 |  |  |  |  |  |  |  |  |
| ME-01 | out | multi  | fan, cafe | 10  |  |  |  |  |  |  |  |  |
| ME-01 | out | multi  | fan, cafe | 5   |  |  |  |  |  |  |  |  |
| ME-01 | out | multi  | fan, cafe | 0   |  |  |  |  |  |  |  |  |
| ME-01 | out | multi  | fan, cafe | -5  |  |  |  |  |  |  |  |  |
| ME-01 | out | multi  | fan, cafe | -10 |  |  |  |  |  |  |  |  |

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


