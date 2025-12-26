
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
| ME-01 | in  | single | dog(3) | 10 |  |  |  |  |  |  |  |  |
| ME-01 | out | multi  | fan, cafe | 10 |  |  |  |  |  |  |  |  |





