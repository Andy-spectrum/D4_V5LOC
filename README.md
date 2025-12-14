# Neuro Project – D4_V5LOC (fMRI + EEG)

## 1. Project Overview

This repository contains our course project for the fMRI & EEG module.

- **Team:** Dorozhkina Irina, Miloserdov Daniil, Jingtao Xu
- **Dataset:**
  - fMRI: **D4_V5LOC** – visual motion localizer (moving vs static dots)
  - EEG: corresponding EEG dataset from the course (1st-level analysis only)
- **Goal (fMRI):**
  - Localize visual motion area **V5/hMT+**
  - Show **group-level effect of moving dots > static dots**
  - Implement:
    - a **simple model**: one-sample t-test at group level
    - a **more complex model**: one-way ANOVA / flexible factorial (block-wise)

- **Goal (EEG):**
  - Perform **1st-level EEG analysis** (per subject) using several signal metrics
    (e.g. ERP amplitudes and/or time–frequency power).

## 2. Course Requirements

From the instructor:

- Each team must work on **both datasets**:
  - fMRI dataset
  - EEG dataset
- **Master students:** work on both **fMRI and EEG**
- **Bachelor students:** work on **fMRI only**
- fMRI part: **1st- and 2nd-level analyses**
- EEG part: **1st-level analyses** only
- Software:
  - fMRI: **MATLAB only** (e.g. SPM)
  - EEG: **MATLAB (EEGLAB/FieldTrip) or Python (MNE)**

Project defenses:
- fMRI presentation: **15.12**
- EEG presentation: **17.12**

Each team: **30 minutes per modality**.

## 3. Data & Task Description

### 3.1 fMRI – D4_V5LOC

- **Experiment:** observation of dynamic (moving) dots while fixating on the center of the screen, with static dots in the baseline condition.
- **Conditions:**
  - `moving`: dynamic/moving dots blocks
  - `static`: static dots / baseline blocks
- **Expected activation:** visual motion area **V5/hMT+** in occipito-temporal cortex.

We will:
1. Preprocess fMRI data in SPM (MATLAB).
2. Build subject-level GLMs.
3. Compute contrasts:
   - `moving > static`
   - (optional) block-wise contrasts: `moving_block_k > static_block_k`
4. Run group-level analyses:
   - One-sample t-test on `moving > static` contrasts.
   - Factorial / ANOVA model on block-wise contrasts.

### 3.2 EEG

- Use the course EEG dataset corresponding to the same / similar visual paradigm.
- Perform 1st-level analysis per subject:
  - Preprocessing (filtering, artifact rejection, etc.)
  - Epoching by condition
  - Baseline correction
  - Compute ERP and/or time–frequency measures
  - Extract condition-specific metrics for later group-level summary.

## 4. Methods Plan

### 4.1 fMRI – 1st-level (per subject)

- **Preprocessing (SPM):**
  - (Optional) Slice timing
  - Realignment + motion parameters
  - Coregistration to structural image
  - Normalization to MNI space
  - Smoothing (e.g. 6–8 mm FWHM)

- **Simple GLM:**
  - Regressors:
    - `moving` (all moving blocks)
    - `static` (all static blocks)
  - Nuisance:
    - 6 motion parameters
  - Main contrast:
    - `moving > static`  → contrast vector `[1 -1]`

- **Block-wise GLM (for factorial model):**
  - Regressors:
    - `moving_block1`, `moving_block2`, ...
    - `static_block1`, `static_block2`, ...
  - Define contrasts for each block:
    - `moving_block_k > static_block_k` (one contrast per block)

### 4.2 fMRI – 2nd-level (group level)

1. **Simple model: One-sample t-test**
   - Input: all subjects' `moving > static` contrast images
   - Output:
     - Group activation map for moving dots
     - Check for V5/hMT+ activation

2. **Complex model: factorial / ANOVA**
   - Use **block-wise contrasts** (`moving_block_k > static_block_k`)
   - Design: flexible factorial / repeated-measures one-way ANOVA
     - Factors:
       - `Subject` (random)
       - `Block` (fixed, levels = number of blocks)
   - Tests:
     - Main effect of block (overall motion effect)
     - Possible differences between early vs late blocks

### 4.3 EEG – 1st-level

(Details will be expanded in `docs/methods_eeg.md`.)

Preliminary plan:
- Choose toolbox: **MATLAB (EEGLAB/FieldTrip)** or **Python (MNE)**.
- Steps:
  1. Import raw EEG
  2. Filtering (e.g. 0.1–40 Hz)
  3. Artifact handling (bad channels, ICA for eye blinks)
  4. Epoch into conditions (e.g. moving vs static)
  5. Baseline correction
  6. Compute:
     - ERP (time-domain)
     - and/or time–frequency power (e.g. alpha/beta)
  7. Extract metrics (mean amplitude / power in predefined time windows and electrodes).

## 5. Repository Structure

See the `fmri/`, `eeg/`, and `docs/` folders for more details:

```text
fmri/
  data/          # data description or small samples (no raw big files)
  scripts/       # MATLAB/SPM scripts for preprocessing and analysis
  results/       # group maps, figures, tables

eeg/
  data/
  scripts/       # EEG analysis scripts (MATLAB or Python)
  results/

docs/
  project_overview.md
  methods_fmri.md
  methods_eeg.md
  timeline.md
  presentation_outline.md
```

## 6. Team Roles (initial idea)

* **Irina Dorozhkina**
  * fMRI preprocessing + 1st-level GLM
  * Help with 2nd-level results & figures

* **Miloserdov Daniil**
  * fMRI 2nd-level (T-test + factorial)
  * Statistical reporting, tables, V5 localization

* **Jingtao Xu**
  * EEG pipeline (toolbox setup, preprocessing, analysis)
  * Contribute to fMRI interpretation & final presentation

> Roles are flexible and can be adjusted as we go.

## 7. Timeline (draft)

* **Week 1:**
  * Set up repository, agree on methods, assign tasks.
* **Week 2:**
  * fMRI preprocessing finished for all subjects.
  * EEG preprocessing pipeline set up and tested on 1–2 subjects.
* **Week 3:**
  * fMRI 1st-level and 2nd-level (simple t-test) completed.
  * EEG 1st-level metrics extracted.
* **Week 4:**
  * fMRI factorial / ANOVA model finished.
  * All figures prepared, presentations drafted.
  * Rehearsal for fMRI (15.12) and EEG (17.12) defenses.

## 8. How to Use This Repository

* Clone:
  ```bash
  git clone <repo-url>
  cd D4_V5LOC
  ```
* For fMRI (MATLAB/SPM):
  * Open MATLAB, add `fmri/scripts/` to path.
  * Run preprocessing & GLM scripts as described in `docs/methods_fmri.md`.
* For EEG:
  * Follow instructions in `docs/methods_eeg.md` to set up environment and run analysis.

## 9. References & Resources

- **SPM:** [https://www.fil.ion.ucl.ac.uk/spm/](https://www.fil.ion.ucl.ac.uk/spm/)
- **EEGLAB:** [https://sccn.ucsd.edu/eeglab/](https://sccn.ucsd.edu/eeglab/)
- **FieldTrip:** [https://www.fieldtriptoolbox.org/](https://www.fieldtriptoolbox.org/)
- **MNE-Python:** [https://mne.tools/](https://mne.tools/)

---

**Last updated:** 2025-11-24
