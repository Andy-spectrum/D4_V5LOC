# fMRI Analysis Methods – D4_V5LOC

## Overview

This document describes the complete fMRI analysis pipeline for the D4_V5LOC visual motion localizer dataset, from raw data to group-level statistical maps.

**Software:** SPM12 (Statistical Parametric Mapping) in MATLAB

**Pipeline stages:**
1. Data organization & inspection
2. Preprocessing
3. 1st-level GLM (subject-level)
4. 2nd-level analysis (group-level) - two models

---

## 1. Data Organization

### Expected data structure

```
D4_V5LOC/
  sub-01/
    anat/
      sub-01_T1w.nii          # Structural (anatomical) image
    func/
      sub-01_task-v5loc_bold.nii   # Functional (BOLD) images
      sub-01_task-v5loc_events.tsv # Timing information
  sub-02/
    ...
```

### Timing information format

The `events.tsv` file should contain:
- `onset`: start time of each block (in seconds)
- `duration`: block duration
- `trial_type`: condition name (`moving` or `static`)

Example:
```
onset	duration	trial_type
0.0	    20.0	    moving
20.0	20.0	    static
40.0	20.0	    moving
...
```

---

## 2. Preprocessing Pipeline

All preprocessing is done in SPM12. Below are the steps and recommended parameters.

### 2.1 Slice Timing Correction (Optional)

**When to use:** If TR is long (>2s) and acquisition is interleaved.

**SPM batch:**
- Module: `Temporal → Slice Timing`
- Parameters:
  - Number of slices: [check your data, typically 30-40]
  - TR (seconds): [e.g., 2.0]
  - TA = TR - (TR/nslices)
  - Slice order: [ascending, descending, or interleaved - check acquisition protocol]
  - Reference slice: middle slice (nslices/2)

**Output:** `asub-XX_task-v5loc_bold.nii` (prefix `a` = slice-time corrected)

---

### 2.2 Realignment (Motion Correction)

**Purpose:** Correct head motion during scanning.

**SPM batch:**
- Module: `Spatial → Realign → Estimate & Reslice`
- Input: functional images (raw or slice-time corrected)
- Parameters:
  - Quality: 0.9
  - Separation: 4 mm
  - Smoothing (FWHM): 5 mm
  - Number of passes: Register to mean
  - Interpolation: 4th Degree B-Spline
  - Wrapping: No wrap
  - Reslice options:
    - Which images to reslice: Mean Image Only (for coregistration)
    - Interpolation: 4th Degree B-Spline

**Output:**
- Realigned images: `rsub-XX_task-v5loc_bold.nii` (prefix `r` = realigned)
- Mean image: `meansub-XX_task-v5loc_bold.nii`
- Motion parameters file: `rp_sub-XX_task-v5loc_bold.txt` (6 columns: 3 translations + 3 rotations)

**Quality check:**
- Plot motion parameters (especially important to flag subjects with excessive motion)
- Threshold: flag if any translation > 3mm or rotation > 3°

---

### 2.3 Coregistration (Functional to Structural)

**Purpose:** Align functional images to structural T1 image.

**SPM batch:**
- Module: `Spatial → Coregister → Estimate`
- Reference image: structural T1 (`sub-XX_T1w.nii`)
- Source image: mean functional (`meansub-XX_task-v5loc_bold.nii`)
- Other images: all realigned functional images
- Parameters:
  - Objective function: Normalized Mutual Information
  - Separation: [4 2]
  - Tolerances: [0.02 0.02 0.02 0.001 0.001 0.001]

**Output:** Updated header (no new files created; transformation stored in header)

**Quality check:** Overlay mean functional on T1 in SPM's "Check Reg" tool

---

### 2.4 Segmentation (of Structural Image)

**Purpose:** Segment T1 into tissue types (GM, WM, CSF) and prepare for normalization.

**SPM batch:**
- Module: `Spatial → Segment`
- Input: structural T1 (`sub-XX_T1w.nii`)
- Tissue options:
  - Save tissue class images: Native Space + DARTEL Imported (if using DARTEL)
  - For each tissue (GM, WM, CSF): Native + Warped
- Output options:
  - Save forward deformation field: Yes (`y_sub-XX_T1w.nii`)

**Output:**
- Tissue probability maps: `c1sub-XX_T1w.nii` (GM), `c2sub-XX_T1w.nii` (WM), etc.
- Forward deformation field: `y_sub-XX_T1w.nii`

---

### 2.5 Normalization to MNI Space

**Purpose:** Warp images into standard MNI space for group analysis.

**SPM batch:**
- Module: `Spatial → Normalise → Write`
- Deformation field: `y_sub-XX_T1w.nii` (from segmentation step)
- Images to write:
  - All coregistered functional images (`rsub-XX_task-v5loc_bold.nii`)
  - Structural T1 (optional, for visualization)
- Voxel sizes: [3 3 3] mm for functional (or [2 2 2] for higher resolution)
- Bounding box: standard MNI (leave as default)
- Interpolation: 4th Degree B-Spline

**Output:** `wrsub-XX_task-v5loc_bold.nii` (prefix `w` = warped/normalized)

**Quality check:** Overlay normalized images on MNI template in SPM

---

### 2.6 Smoothing

**Purpose:** Increase signal-to-noise ratio and improve group-level statistics validity.

**SPM batch:**
- Module: `Spatial → Smooth`
- Input: normalized functional images (`wrsub-XX_task-v5loc_bold.nii`)
- FWHM (mm): [6 6 6] or [8 8 8]
  - Recommendation: 6mm for focal activation (V5), 8mm for exploratory whole-brain

**Output:** `swrsub-XX_task-v5loc_bold.nii` (prefix `s` = smoothed)

---

### Summary of preprocessing outputs

Final preprocessed functional images for GLM:
```
swrsub-01_task-v5loc_bold.nii
swrsub-02_task-v5loc_bold.nii
...
```

Motion regressors for GLM:
```
rp_sub-01_task-v5loc_bold.txt
rp_sub-02_task-v5loc_bold.txt
...
```

---

## 3. First-Level GLM (Subject-Level Analysis)

For each subject, we build a General Linear Model to estimate condition-specific brain responses.

### 3.1 Model Specification (Simple Model)

**Purpose:** Model the two main conditions: `moving` and `static`.

**SPM batch:**
- Module: `Stats → fMRI model specification`
- Directory: create a new folder for each subject (e.g., `fmri/results/first_level/sub-01/`)
- Timing parameters:
  - Units for design: **Seconds**
  - Interscan interval (TR): [e.g., 2.0 seconds]
  - Microtime resolution: 16 (default)
  - Microtime onset: 8 (default, or middle slice from slice timing)

**Session specifications:**
- Scans: select all preprocessed functional images for this subject (`swrsub-XX_task-v5loc_bold.nii`)
- Conditions (create 2 conditions):
  1. **Condition 1:**
     - Name: `moving`
     - Onsets: [vector of onset times for all moving blocks]
     - Durations: [vector of durations, e.g., 20 20 20...]
  2. **Condition 2:**
     - Name: `static`
     - Onsets: [vector of onset times for all static blocks]
     - Durations: [vector of durations]
- Multiple regressors: load motion parameters file (`rp_sub-XX_task-v5loc_bold.txt`)
  - This adds 6 nuisance regressors (3 translations + 3 rotations)

**Other parameters:**
- High-pass filter: 128 seconds (default, removes slow drifts)
- Autocorrelation modeling: AR(1) (default)

**Output:** `SPM.mat` file containing the model specification

---

### 3.2 Model Estimation

**SPM batch:**
- Module: `Stats → Model estimation`
- Input: `SPM.mat` from previous step
- Method: Classical (ReML)

**Output:** Updated `SPM.mat` with estimated parameters (betas) for each regressor

Files created:
- `beta_0001.nii` (parameter estimate for moving condition)
- `beta_0002.nii` (parameter estimate for static condition)
- `beta_0003.nii` to `beta_0008.nii` (motion parameters)
- `ResMS.nii` (residual mean squares - error variance)
- `RPV.nii` (resels per voxel)

---

### 3.3 Contrast Definition (Simple Model)

**Purpose:** Define statistical tests to compare conditions.

**SPM batch:**
- Module: `Stats → Contrast Manager`
- Input: `SPM.mat` from estimation

**Contrasts to define:**

1. **Moving > Static**
   - Name: `moving_vs_static`
   - Type: T-contrast
   - Weights: `[1 -1 0 0 0 0 0 0]`
     - (1 for moving, -1 for static, 0 for motion regressors)
   - This tests for greater activation during moving vs static dots

2. **Static > Moving** (optional, for completeness)
   - Name: `static_vs_moving`
   - Type: T-contrast
   - Weights: `[-1 1 0 0 0 0 0 0]`

3. **Main effect of task** (optional)
   - Name: `task_vs_baseline`
   - Type: F-contrast
   - Weights: `[1 1 0 0 0 0 0 0]` (or as F-contrast matrix)

**Output:** Contrast images
- `con_0001.nii` (moving > static contrast)
- `spmT_0001.nii` (corresponding t-statistic map)
- `con_0002.nii`, `spmT_0002.nii` (for second contrast, etc.)

---

### 3.4 Block-Wise Model (for Factorial Design)

If the experiment has multiple blocks (e.g., 4 moving blocks, 4 static blocks), we can model each block separately.

**Purpose:** Allow for more complex 2nd-level designs testing block effects.

**Model specification differences:**
- Create separate regressors for each block:
  - `moving_block1`, `moving_block2`, `moving_block3`, `moving_block4`
  - `static_block1`, `static_block2`, `static_block3`, `static_block4`
- Each regressor gets the onset time and duration for that specific block only

**Contrasts to define (for each block):**
- `moving_block1 > static_block1`: weights `[1 -1 0 0 0 0 0 0 0 0 0 0 0 0]`
- `moving_block2 > static_block2`: weights `[0 0 1 -1 0 0 0 0 0 0 0 0 0 0]`
- `moving_block3 > static_block3`: weights `[0 0 0 0 1 -1 0 0 0 0 0 0 0 0]`
- `moving_block4 > static_block4`: weights `[0 0 0 0 0 0 1 -1 0 0 0 0 0 0]`

(Assuming 8 condition regressors + 6 motion regressors = 14 total)

**Output:** One contrast image per block comparison (4 con images per subject)

---

## 4. Second-Level Analysis (Group-Level)

### 4.1 Simple Model: One-Sample T-Test

**Purpose:** Test if the `moving > static` effect is consistent across subjects at the group level.

**SPM batch:**
- Module: `Stats → Factorial design specification`
- Directory: create folder for group analysis (e.g., `fmri/results/second_level_simple/`)
- Design: **One-sample t-test**
- Scans: select `con_0001.nii` from all subjects
  - e.g., `sub-01/con_0001.nii`, `sub-02/con_0001.nii`, ...

**Model estimation:**
- Module: `Stats → Model estimation`
- Input: `SPM.mat` from design specification

**Contrast:**
- Module: `Stats → Contrast Manager`
- Name: `group_moving_vs_static`
- Type: T-contrast
- Weights: `[1]` (since there's only one group mean)

**Output:** `spmT_0001.nii` (group-level t-map)

**Thresholding & results:**
- Module: `Stats → Results`
- Apply FWE (Family-Wise Error) correction: p < 0.05 FWE-corrected
- Or use cluster-level inference: p < 0.001 uncorrected, cluster extent threshold based on expected voxels
- Expected result: Significant cluster in V5/hMT+ region (posterior temporal cortex, near junction of occipital and temporal lobes)

---

### 4.2 Complex Model: Flexible Factorial (Block-Wise)

**Purpose:** Test block effects and account for repeated measures (blocks within subjects).

**Design:** Flexible factorial with two factors:
- **Factor 1 (Subject):** random effect, levels = number of subjects (e.g., 15)
- **Factor 2 (Block):** fixed effect, levels = number of blocks (e.g., 4)

**SPM batch:**
- Module: `Stats → Factorial design specification`
- Directory: create folder (e.g., `fmri/results/second_level_factorial/`)
- Design: **Flexible factorial**

**Factor specifications:**
- Factor 1:
  - Name: `subject`
  - Independence: Yes (subjects are independent)
  - Variance: Equal
  - Grand mean scaling: No
  - ANOVA: No
- Factor 2:
  - Name: `block`
  - Independence: No (blocks within subject are dependent)
  - Variance: Equal
  - Grand mean scaling: No
  - ANOVA: Yes (we want to test block effect)

**Specify cells (one cell per subject × block combination):**
- For each subject and each block, load the corresponding contrast image:
  - Sub-01, Block-1: `sub-01/con_0001.nii` (moving_block1 > static_block1)
  - Sub-01, Block-2: `sub-01/con_0002.nii` (moving_block2 > static_block2)
  - ...
  - Sub-02, Block-1: `sub-02/con_0001.nii`
  - Sub-02, Block-2: `sub-02/con_0002.nii`
  - ...

Total scans: n_subjects × n_blocks (e.g., 15 × 4 = 60 images)

**Model estimation:**
- Module: `Stats → Model estimation`

**Contrasts to test:**

1. **Main effect of blocks (any motion effect):**
   - Type: F-contrast
   - Contrast matrix: test for any difference across blocks
   - (Constructed automatically if "ANOVA" option was selected for Factor 2)

2. **Linear trend across blocks (e.g., habituation or attention change):**
   - Type: T-contrast
   - Weights: [adjusted for number of subjects and blocks]
   - Example for 4 blocks: weights that increase linearly across block regressors

3. **Early vs late blocks:**
   - Type: T-contrast
   - Compare mean of first 2 blocks vs mean of last 2 blocks

**Output:** Statistical maps for each contrast (F-maps or T-maps)

**Thresholding & interpretation:**
- Main effect of block → activation that is consistent across all blocks
- Linear trend → areas showing increasing or decreasing response over time
- Early vs late → differences in habituation or attentional state

---

## 5. Visualization and Reporting

### 5.1 Overlaying Results on Anatomy

Use SPM's **Results** interface:
- Load `SPM.mat` from 2nd-level folder
- Select contrast
- Set threshold (e.g., p < 0.05 FWE or p < 0.001 unc + cluster extent)
- Click **overlay → sections** to display on standard brain slices
- Save figures (use SPM's print function or screenshot)

### 5.2 Extracting Peak Coordinates

In Results window:
- View the table of significant clusters
- Note MNI coordinates of peak voxels
- Report: cluster size (# voxels), peak T or F value, p-value, MNI coordinates [x, y, z]

Example:
> Significant activation was observed in right V5/hMT+ (peak: [48, -72, 6], cluster size = 347 voxels, T = 8.45, p < 0.001 FWE-corrected).

### 5.3 Region of Interest (ROI) Analysis (Optional)

If needed, extract mean signal from V5/hMT+ ROI:
- Define ROI using anatomical atlas or functional localizer peak
- Use `marsbar` toolbox in SPM or custom scripts
- Extract beta values or contrast values per subject
- Plot individual subject data for interpretation

---

## 6. Quality Control Checklist

- [ ] All subjects' functional images preprocessed without errors
- [ ] Motion parameters reviewed (no subject with excessive motion)
- [ ] Coregistration visually checked (functional aligned to structural)
- [ ] Normalization visually checked (subjects aligned to MNI template)
- [ ] 1st-level design matrices reviewed (regressors correctly specified)
- [ ] 1st-level contrasts computed for all subjects
- [ ] 2nd-level models include correct number of subjects
- [ ] Statistical maps show expected activation in V5/hMT+ region
- [ ] Results thresholded appropriately (multiple comparisons corrected)
- [ ] Peak coordinates and statistics extracted and reported

---

## 7. Troubleshooting

**Issue:** Realignment fails or produces large motion parameters
- Solution: Check data quality, consider excluding subject, or use more stringent motion correction

**Issue:** Coregistration misalignment
- Solution: Manually initialize coregistration, check for image artifacts

**Issue:** No significant activation in 2nd-level analysis
- Solution: Check 1st-level contrasts, verify timing parameters, consider lowering threshold or using small volume correction

**Issue:** SPM crashes or runs out of memory
- Solution: Increase MATLAB memory, process fewer images at once, use implicit masking

---

## 8. References

- Friston, K. J., et al. (1995). Statistical parametric maps in functional imaging: A general linear approach. Human Brain Mapping, 2(4), 189-210.
- Ashburner, J., & Friston, K. J. (2005). Unified segmentation. NeuroImage, 26(3), 839-851.
- Worsley, K. J., et al. (1996). A unified statistical approach for determining significant signals in images of cerebral activation. Human Brain Mapping, 4(1), 58-73.

**SPM Manual:** [https://www.fil.ion.ucl.ac.uk/spm/doc/manual.pdf](https://www.fil.ion.ucl.ac.uk/spm/doc/manual.pdf)
