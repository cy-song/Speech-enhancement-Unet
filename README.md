
# Speech Enhancement with U-Net (Inference & Evaluation Pipeline)

This repository presents an **inference and evaluation pipeline for speech enhancement** using a pre-trained **U-Net-based magnitude masking model**.

The focus of this project is **not model novelty**, but **engineering correctness and evaluation reliability**, including:

- Fixed-SNR testing for stable comparison
- Alignment between training and inference pipelines
- Use of **real-world recorded noises**
- Objective metrics (PESQ / STOI) **and** Noise Reduction analysis
- Diagnosis of metric–perceptual mismatch

This project is designed for **Audio AI / ML Engineer** roles and emphasizes **reproducibility, analysis, and practical validation**.

---

##  Pipeline Overview

**Inference & Evaluation Flow**

1. Load clean speech (LibriSpeech or user-uploaded)
2. Load pre-trained U-Net model
3. Build noise bank from uploaded real noises
4. Add multiple noises at a **fixed SNR**
5. STFT → U-Net mask → ISTFT
6. Evaluate:
   - PESQ
   - STOI
   - Noise Reduction (NR, dB)
7. Subjective listening comparison

---

##  Repository Structure

```
├── step1_load_librispeech.py # Load clean speech reference
├── step2_config.py # Configuration
├── step3_load_model.py # Load trained UNet model
├── step4_build_noise_bank.py # Build noise bank
├── step5_build_noisy_dataset_fixed_snr.py #@ fixed SNR (test condition)
├── step6_infer_eval_on_fixed_snr.py # NOTE: fixed SNR is defined in the noise mixing stage; this script runs inference/eval under that condition.
├── step7_metrics.py # PESQ / STOI / NR
├── step8_main.py # Main evaluation script
└── README.md
```

---

##  How to Run (Colab-friendly)

### 1️. Install dependencies
```bash
pip install torch torchaudio librosa pesq pystoi matplotlib
```
### 2️. Prepare inputs
Clean speech:

- LibriSpeech (auto download), or  
- Upload your own clean speech file

Noise:

- Upload one or multiple real recorded noise files  
  (.wav, .mp3, .m4a)

### 3️. Run main program

Execute `step8_main.py` (or the corresponding notebook).

The program will:

- Loop over SNR = `[10, 5, 0, -5, -10]`
- Output PESQ / STOI / Noise Reduction
- Play Clean / Noisy / Enhanced audio for listening

---

## 📊 Evaluation Metrics

### Objective Metrics

- PESQ (WB): perceptual speech quality  
- STOI: speech intelligibility  

Noise Reduction (NR):

```
NR(dB) = 10 * log10( noise_power_noisy / noise_power_enhanced )
```

### Subjective Evaluation

Direct listening comparison is used to interpret cases where  
objective metrics and perceptual quality diverge.

---

## 🔍 Problem Analysis & Lessons Learned (Key Takeaways)

### 1️⃣ SNR Control Is Critical (Train vs Test Mismatch)

**Problem**

- Training uses random SNR sampling (`CFG["SNR_LIST"]`) for data augmentation  
- Early testing reused the same random SNR logic  
- This caused high variance and unstable PESQ / STOI, making results non-comparable  

**Solution**

- Clearly separate responsibilities:
  - **Training**: random SNR (robustness)
  - **Testing**: fixed SNR (evaluation stability)
- Introduced `infer_once_fixed_snr()` for testing
- Explicitly specify SNR levels:
  - `10, 5, 0, -5, -10 dB`

✅ Ensures reproducible and comparable evaluation across SNRs.

---

### 2️⃣ Train / Test Pipeline Must Be Strictly Aligned

**Problem**

- Early inference used a legacy noise injection method (`noise_wave`)
- Training data was generated using `add_all_noises_with_snr()`
- Result: hidden domain shift — the model was evaluated on an unseen data distribution

**Solution**

- Fully aligned inference pipeline with training:
  - Multi-noise mixing
  - RMS normalization
  - STFT → U-Net mask → ISTFT
- Removed all legacy test functions  
  (e.g. `test_multi_noise_once_*`)

✅ Eliminates unintended distribution mismatch between training and testing.

---

### 3️⃣ Objective Metrics May Decrease While Perceptual Quality Improves

**Observation**

In some low-SNR cases:

- PESQ / STOI decreased
- Subjective noise suppression clearly improved

**Analysis**

Strong noise suppression can introduce:

- Speech distortion
- Over-suppression of low-energy speech components

PESQ / STOI penalize distortion more than residual noise.

**Mitigation**

- Added **Noise Reduction (NR)** metric to explicitly quantify noise suppression
- Combined:
  - Objective metrics
  - Listening-based diagnosis

✅ Metrics must be interpreted, not blindly optimized.

---

### 4️⃣ Real-World Noises Are Essential for Practical Validation

**Finding**

Models performing well on synthetic noises may fail on:

- Keyboard noise
- Environmental recordings
- Device-specific artifacts

**Approach**

- Built `noise_bank` from user-recorded real noises
- Mixed multiple noises to simulate realistic environments

✅ Improves confidence in real-world deployment scenarios.

---

## 🎯 Key Takeaways

- Speech enhancement performance is **pipeline-sensitive**, not just model-dependent
- Evaluation stability requires explicit control of SNR
- Train/test alignment is mandatory to avoid silent failure
- Objective metrics and listening tests must be used together
- Engineering discipline matters as much as model architecture

---

## 📌 Notes

- This repository focuses on **inference and evaluation** using a pre-trained model
- Model training is intentionally separated
- The code is designed for **clarity and reproducibility**, not production deployment

---

## 👤 Author Intent

This project demonstrates my approach to Audio AI engineering:

- Start from real-world audio problems
- Diagnose failures using both metrics and listening
- Iterate with controlled experiments
- Prioritize correctness and interpretability over novelty

