# fMRI Data Directory
---

# fMRI Group-Level Analysis of Motion Processing (Dataset D4_V5LOC)

## Project Overview

This project presents a complete first-level and second-level fMRI analysis pipeline implemented in **SPM**.
The goal is to identify brain regions involved in **visual motion processing**, with a particular focus on area **V5**, using multiple group-level statistical models.

We compare a **simple group model (one-sample t-test)** with **more complex factorial models** to illustrate how different second-level designs affect statistical inference while relying on the same first-level contrasts.

---

## Dataset Description (Dataset #4: D4_V5LOC)

**Experiment**
Participants observed **dynamic (moving) dots** while maintaining fixation at the center of the screen.
Periods of **static dots** served as the implicit baseline condition.

**Experimental Conditions**

* Moving dots (task condition)
* Static dots (baseline)

**Expected Activation**

* Visual motion–sensitive cortex, primarily **area V5**

**Dataset Purpose**
The dataset is designed as a **functional localizer** for motion-sensitive visual regions and is well suited for:

* one-sample group analyses
* block-wise modeling
* comparison of different second-level GLM designs 

---

## Analysis Pipeline

### First-Level Analysis (Single Subject)

For each subject:

1. A **voxel-wise GLM** was specified using:

   * Task regressors obtained by convolving block onsets with the **canonical HRF**
   * Motion parameters as nuisance regressors
   * High-pass filtering to remove low-frequency drift
   * AR(1) noise modeling and pre-whitening (SPM default)

2. Subject-level **contrast images (`con_*.nii`)** were computed for:

   * Moving dots > baseline

These contrast images form the input to all group-level analyses.

---

### Second-Level (Group-Level) Analyses

Several group-level models were implemented using the same subject-level contrasts:

#### 1. One-Sample t-Test

* Tests whether the average contrast value across subjects differs from zero
* Directly addresses the question:

  > *Is there a consistent group-level activation to moving dots?*

This model provides a clear and interpretable baseline result.

---

#### 2. Flexible Factorial Model

* Subject treated as a factor
* Block-wise or condition-specific contrasts entered per subject
* Allows modeling of:

  * within-subject variance
  * block-related effects
  * more complex dependency structures

This model demonstrates how richer designs can be used when the experimental structure justifies them.

---

#### 3. Full Factorial

* Used to illustrate alternative second-level formulations
* Enables comparison across modeled blocks or conditions
* Serves a methodological purpose rather than a necessity for this dataset

---

## Statistical Inference

* **T-contrasts** were used to test directional hypotheses (activation and deactivation)

### Multiple Comparisons Correction

Results were examined using:

* uncorrected voxel-level thresholds (for illustration)
* **Family-Wise Error (FWE) correction**, based on **Random Field Theory (RFT)**, as implemented in SPM

---

## Key Findings

* All group-level models consistently revealed activation in **area V5**, confirming its role in motion processing
* More complex models redistributed variance across regressors but did not contradict the core finding
* The one-sample t-test provided the most parsimonious and interpretable result for this dataset

---

## Tools and Software

* **SPM12 / SPM25**
* MATLAB
* Statistical Parametric Mapping (GLM, contrasts, RFT-based inference)

---

## Notes

This project emphasizes **conceptual clarity of GLM-based fMRI analysis**, rather than optimization for a specific experimental contrast.
It is intended as both a **scientific analysis** and a **didactic demonstration** of SPM’s first- and second-level modeling framework.
Detailed results and figures are available in [results.md](results.md).



