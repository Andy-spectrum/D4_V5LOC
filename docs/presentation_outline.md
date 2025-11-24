# Presentation Outlines

This document provides detailed outlines for both the fMRI and EEG presentations.

**Time limit:** 30 minutes per modality
**Recommended structure:** ~20 minutes presentation + ~10 minutes Q&A

---

## fMRI Presentation Outline (Dec 15)

### Slide 1: Title Slide (30 seconds)

**Content:**
- Project title: "Localizing Visual Motion Area V5/hMT+ Using fMRI"
- Dataset: D4_V5LOC
- Team members: Irina Dorozhkina, Daniil Miloserdov, Jingtao Xu
- Date: December 15, 2025

---

### Slide 2: Introduction - Visual Motion Processing (2 minutes)

**Speaker:** Daniil

**Content:**
- What is visual motion perception?
  - Fundamental ability to detect and process moving objects
  - Critical for navigation, object tracking, predicting trajectories
- Neural basis: specialized brain regions for motion processing
- Introduction to **V5/hMT+ (middle temporal area)**:
  - Located in occipito-temporal cortex
  - Selectively responsive to visual motion
  - First identified in non-human primates, homologous area in humans
  - Lesions → motion blindness (akinetopsia)

**Visual aids:**
- Brain diagram showing V5/hMT+ location
- Example of motion stimulus (moving dots)

---

### Slide 3: Study Aims & Hypotheses (1 minute)

**Speaker:** Daniil

**Content:**
- **Primary aim:** Localize V5/hMT+ using a visual motion localizer task
- **Hypothesis:** Greater BOLD activation in V5/hMT+ for moving vs static dots
- **Secondary aim:** Compare two statistical approaches:
  1. Simple model: one-sample t-test
  2. Complex model: flexible factorial design with block-wise effects

---

### Slide 4: Experimental Paradigm (2 minutes)

**Speaker:** Irina

**Content:**
- **Task:** Visual motion localizer (D4_V5LOC)
- **Design:** Block design
  - Moving dots: dynamic dot pattern
  - Static dots: stationary dot pattern (baseline)
  - Block duration: ~20 seconds each
  - Number of blocks: [specify, e.g., 4 moving + 4 static]
- **Participants:** [N subjects, e.g., 15 healthy adults]
- **Fixation:** Participants maintained central fixation throughout

**Visual aids:**
- Schematic of task design (block sequence)
- Example frames from moving and static conditions

---

### Slide 5: MRI Acquisition Parameters (1 minute)

**Speaker:** Irina

**Content:**
- Scanner: [e.g., 3T Siemens]
- Functional images:
  - TR = [e.g., 2.0s]
  - TE = [e.g., 30ms]
  - Voxel size = [e.g., 3×3×3 mm³]
  - Number of slices = [e.g., 35]
- Structural image (T1-weighted):
  - Resolution: [e.g., 1×1×1 mm³]

---

### Slide 6: Preprocessing Pipeline (2 minutes)

**Speaker:** Irina

**Content:**
- Software: SPM12
- Steps:
  1. Realignment (motion correction)
  2. Coregistration (functional to structural)
  3. Segmentation (tissue probability maps)
  4. Normalization to MNI space
  5. Smoothing (6 mm FWHM)

**Visual aids:**
- Flowchart of preprocessing steps
- Example: before/after normalization (overlay on MNI template)

**Quality control:**
- Motion parameters checked (all subjects < 3mm translation)
- Visual inspection of coregistration and normalization

---

### Slide 7: First-Level Analysis - Simple Model (2 minutes)

**Speaker:** Daniil

**Content:**
- **GLM design:**
  - Regressors:
    - Moving blocks (convolved with HRF)
    - Static blocks (convolved with HRF)
    - 6 motion parameters (nuisance regressors)
  - High-pass filter: 128s
- **Contrast:**
  - `moving > static` (weights: [1 -1 0 0 0 0 0 0])
- **Output:** One contrast image per subject (`con_0001.nii`)

**Visual aids:**
- Example design matrix (one subject)
- Example 1st-level activation map (one subject)

---

### Slide 8: First-Level Analysis - Block-Wise Model (2 minutes)

**Speaker:** Daniil

**Content:**
- **Purpose:** Allow for more flexible group-level design
- **GLM design:**
  - Separate regressors for each block:
    - `moving_block1`, `moving_block2`, ...
    - `static_block1`, `static_block2`, ...
  - Motion parameters
- **Contrasts:**
  - `moving_block1 > static_block1`
  - `moving_block2 > static_block2`
  - (etc. for all blocks)
- **Output:** Multiple contrast images per subject

**Visual aids:**
- Block-wise design matrix
- Illustration of contrast structure

---

### Slide 9: Second-Level Analysis - Simple Model (3 minutes)

**Speaker:** Daniil

**Content:**
- **Design:** One-sample t-test
- **Input:** All subjects' `moving > static` contrast images
- **Statistical threshold:**
  - FWE-corrected p < 0.05 (whole-brain)
  - Or cluster-level inference: p < 0.001 uncorrected, cluster extent threshold

**Results:**
- Significant activation in **V5/hMT+ bilaterally**
- Peak coordinates:
  - Left V5: [x, y, z] (MNI coordinates)
  - Right V5: [x, y, z]
- Cluster size: [number of voxels]
- Peak T-value: [T statistic]

**Visual aids:**
- Group activation map (axial, coronal, sagittal views)
- Glass brain plot showing V5/hMT+ activation
- Table of peak activations

---

### Slide 10: Second-Level Analysis - Factorial Model (3 minutes)

**Speaker:** Daniil

**Content:**
- **Design:** Flexible factorial
  - Factor 1: Subject (random)
  - Factor 2: Block (fixed)
- **Input:** Block-wise contrast images from all subjects
- **Tests:**
  - Main effect of motion (across all blocks)
  - Block-by-block differences (e.g., early vs late blocks)

**Results:**
- Main effect confirms V5/hMT+ activation
- [Optional] Block effects: evidence of habituation or attentional changes?

**Visual aids:**
- Factorial design schematic
- Statistical maps (F-contrast for main effect, T-contrast for block comparisons)
- Comparison with simple model results

---

### Slide 11: Region of Interest (ROI) Analysis (2 minutes)

**Speaker:** Daniil

**Content:**
- **ROI definition:** V5/hMT+ from group activation peak
- **Extracted values:** Mean beta or contrast values per subject
- **Visualization:** Bar plot or box plot showing effect size

**Results:**
- Consistent individual-level effects
- Effect size (Cohen's d): [value]

**Visual aids:**
- Bar graph: moving vs static (group mean ± SEM)
- Scatter plot: individual subjects' contrast values

---

### Slide 12: Discussion - Interpretation (2 minutes)

**Speaker:** Daniil

**Content:**
- **Summary of findings:**
  - Successfully localized V5/hMT+ using visual motion task
  - Robust activation for moving > static contrast
  - Consistent results across both statistical models
- **Interpretation:**
  - V5/hMT+ is selectively responsive to visual motion (as expected)
  - Validates the functional localizer approach
- **Comparison with literature:**
  - Our coordinates match previously reported V5/hMT+ locations
  - Typical coordinates: ±45, -70, 0 (approximate)

---

### Slide 13: Limitations & Future Directions (1 minute)

**Speaker:** Irina

**Content:**
- **Limitations:**
  - Small sample size (if applicable)
  - Block design (less sensitive than event-related design)
  - No behavioral measures (attention, task engagement)
- **Future directions:**
  - Individual differences in V5 size/location
  - Connectivity analysis (V5 interactions with other visual areas)
  - Combine with behavioral measurements

---

### Slide 14: Conclusions (1 minute)

**Speaker:** Daniil

**Content:**
- V5/hMT+ successfully localized using fMRI
- Robust activation for visual motion processing
- Both simple and complex statistical models yield consistent results
- Demonstrates the power of functional localizers for identifying specialized brain regions

---

### Slide 15: Acknowledgments & Questions (30 seconds)

**Speaker:** Irina

**Content:**
- Thanks to instructors, course staff
- Data source acknowledgment
- **Open for questions**

---

## fMRI Q&A Preparation

**Anticipated questions:**

1. **Why use block design instead of event-related?**
   - Answer: Block designs have higher power for detecting sustained activation. For a localizer task, this is appropriate.

2. **How did you correct for multiple comparisons?**
   - Answer: FWE correction at whole-brain level, or cluster-level inference with uncorrected height threshold + cluster extent.

3. **What if V5 activation was not found?**
   - Answer: Check preprocessing, verify timing parameters, consider individual variability, possibly use small volume correction.

4. **What is the difference between the two 2nd-level models?**
   - Answer: Simple model tests overall effect; factorial model allows testing for block-specific effects (e.g., habituation).

5. **How many subjects were excluded due to motion?**
   - Answer: [State actual number, if any; explain criteria for exclusion].

6. **What are the functional implications of V5 activation?**
   - Answer: V5 is critical for motion perception; damage leads to motion blindness. Used in applications like studying disorders (e.g., autism, schizophrenia) or visual training.

---

## EEG Presentation Outline (Dec 17)

### Slide 1: Title Slide (30 seconds)

**Content:**
- Project title: "EEG Analysis of Visual Motion Processing"
- Dataset: EEG data from visual motion paradigm
- Team members: Jingtao Xu (presenter), Irina Dorozhkina, Daniil Miloserdov
- Date: December 17, 2025

---

### Slide 2: Introduction - EEG Basics (2 minutes)

**Speaker:** Jingtao

**Content:**
- **What is EEG?**
  - Non-invasive measure of electrical brain activity
  - High temporal resolution (milliseconds)
  - Records scalp potentials from neuronal activity
- **Why EEG for visual motion?**
  - Complements fMRI (fMRI = spatial, EEG = temporal)
  - Can measure rapid visual processing stages
  - Sensitive to oscillatory brain rhythms (alpha, beta, gamma)

**Visual aids:**
- Diagram of EEG cap and electrode placement
- Comparison: fMRI (spatial) vs EEG (temporal)

---

### Slide 3: Event-Related Potentials (ERPs) (1.5 minutes)

**Speaker:** Jingtao

**Content:**
- **ERP definition:** Time-locked average of EEG responses
- **Visual ERP components:**
  - **P1 (~100 ms):** Early visual processing, attention
  - **N1 (~150-200 ms):** Feature detection, selective attention
  - **P2 (~200-300 ms):** Higher-order processing
- **Expected effects:**
  - Enhanced P1/N1 for moving vs static stimuli (attention capture)
  - Occipital electrodes (Oz, O1, O2)

**Visual aids:**
- Example ERP waveform with labeled components
- Topographic map showing occipital distribution

---

### Slide 4: Oscillatory Activity (1.5 minutes)

**Speaker:** Jingtao

**Content:**
- **Brain oscillations:** Rhythmic neural activity at specific frequencies
- **Relevant frequency bands:**
  - **Alpha (8-12 Hz):** Inhibition, idling; suppressed during visual attention
  - **Beta (15-25 Hz):** Sensory processing, feedback mechanisms
  - **Gamma (>30 Hz):** Local processing, binding (not focus here)
- **Expected effects:**
  - Alpha suppression during moving dots (active processing)
  - Possible beta modulation

**Visual aids:**
- Frequency band diagram
- Example time-frequency plot

---

### Slide 5: Study Design & Participants (1 minute)

**Speaker:** Jingtao

**Content:**
- **Same paradigm as fMRI:**
  - Moving dots vs static dots, block design
  - Participants: [N subjects]
- **EEG setup:**
  - [Number] channels (e.g., 32-channel or 64-channel)
  - Sampling rate: [e.g., 500 Hz or 1000 Hz]
  - Reference: [e.g., average reference or mastoid]

---

### Slide 6: Preprocessing Pipeline (3 minutes)

**Speaker:** Jingtao

**Content:**
- **Software:** [EEGLAB, FieldTrip, or MNE-Python]
- **Steps:**
  1. **Filtering:**
     - High-pass: 0.1 Hz (remove drift)
     - Low-pass: 40 Hz (remove high-frequency noise)
     - Notch filter: 50 Hz (power line noise)
  2. **Re-referencing:** Average reference
  3. **ICA (Independent Component Analysis):**
     - Identify and remove eye blinks, eye movements, muscle artifacts
     - [Number] components removed on average
  4. **Epoching:**
     - Segment into trials: -0.5 to 2.0 seconds relative to block onset
     - Baseline correction: -200 to 0 ms
  5. **Artifact rejection:**
     - Amplitude threshold: ±100 μV
     - [Number] epochs rejected on average

**Visual aids:**
- Preprocessing flowchart
- Example: raw vs cleaned data
- ICA component examples (blink, eye movement)

---

### Slide 7: ERP Results - Waveforms (3 minutes)

**Speaker:** Jingtao

**Content:**
- **Grand average ERPs (all subjects combined):**
  - Electrode Oz (occipital midline)
  - Overlay: moving (blue) vs static (red)
- **Key findings:**
  - **P1 component (~100 ms):**
    - Peak amplitude: Moving = [value], Static = [value]
    - [Significant difference?]
  - **N1 component (~150-200 ms):**
    - Enhanced negativity for moving condition
    - Peak amplitude: Moving = [value], Static = [value]
  - **P2 component (~200-300 ms):**
    - Peak amplitude: [values]

**Visual aids:**
- ERP waveform plot (Oz, moving vs static)
- Topographic maps at P1, N1, P2 latencies

---

### Slide 8: ERP Results - Statistical Comparison (2 minutes)

**Speaker:** Jingtao

**Content:**
- **Quantitative analysis:**
  - Mean amplitude in predefined time windows:
    - P1 (90-120 ms), N1 (150-200 ms), P2 (200-300 ms)
  - Electrode: Oz
- **Results:**
  - [e.g., "N1 amplitude significantly larger for moving vs static, p < 0.05"]
  - Effect size: [Cohen's d value]

**Visual aids:**
- Bar plot: P1, N1, P2 amplitudes (moving vs static, group mean ± SEM)
- Scatter plot: individual subject data

---

### Slide 9: Time-Frequency Results (3 minutes)

**Speaker:** Jingtao

**Content:**
- **Time-frequency analysis:**
  - Method: Wavelet convolution (or STFT)
  - Frequency range: 4-40 Hz
  - Time range: -0.5 to 2.0 seconds
  - Baseline: -200 to 0 ms

- **Alpha band (8-12 Hz):**
  - Suppression during stimulus presentation (both conditions)
  - Stronger suppression for moving dots?
  - Time window: 0-500 ms

- **Beta band (15-25 Hz):**
  - Modulation during stimulus
  - Differences between conditions?

**Visual aids:**
- Time-frequency plots (spectrograms) for moving and static conditions
- Difference plot (moving - static)
- Bar plot: mean alpha power (moving vs static)

---

### Slide 10: Summary of EEG Metrics (2 minutes)

**Speaker:** Jingtao

**Content:**
- **Table of extracted metrics (per subject):**
  - P1, N1, P2 amplitudes
  - Alpha power (8-12 Hz, 0-500 ms)
  - Beta power (15-25 Hz, 0-500 ms)

- **Key findings:**
  - Enhanced N1 for moving dots (early motion detection)
  - Alpha suppression (attentional engagement)
  - [Beta modulation, if significant]

**Visual aids:**
- Summary table (group statistics)
- Comparison plot (metrics side-by-side for both conditions)

---

### Slide 11: Correlation with fMRI (Optional, 1 minute)

**Speaker:** Jingtao

**Content:**
- **Linking EEG and fMRI:**
  - fMRI shows *where* (V5/hMT+)
  - EEG shows *when* (N1 component, ~150-200 ms)
- **Interpretation:**
  - N1 likely reflects early processing in V5 and related areas
  - Alpha suppression reflects increased cortical excitability

---

### Slide 12: Discussion (2 minutes)

**Speaker:** Jingtao

**Content:**
- **Summary:**
  - EEG reveals temporal dynamics of visual motion processing
  - Enhanced N1 component for moving vs static dots
  - Alpha suppression indicates attentional engagement
- **Interpretation:**
  - Rapid detection of motion (within 200 ms)
  - Consistent with visual hierarchy (V1 → V5)
- **Complementarity with fMRI:**
  - fMRI: localized activation in V5/hMT+
  - EEG: timing of activation and oscillatory dynamics

---

### Slide 13: Limitations & Future Directions (1 minute)

**Speaker:** Jingtao

**Content:**
- **Limitations:**
  - 1st-level analysis only (no group-level statistics in presentation)
  - Limited spatial resolution (cannot pinpoint exact brain regions)
  - Artifact removal challenges (ICA not perfect)
- **Future directions:**
  - Group-level statistics on ERP/TFR metrics
  - Source localization (e.g., using beamforming or dipole fitting)
  - Multimodal fusion with fMRI (EEG-fMRI integration)

---

### Slide 14: Conclusions (1 minute)

**Speaker:** Jingtao

**Content:**
- EEG provides high temporal resolution view of visual motion processing
- Enhanced N1 response for moving stimuli (early motion detection)
- Alpha suppression reflects attentional modulation
- Complements fMRI findings (spatial and temporal perspectives combined)

---

### Slide 15: Acknowledgments & Questions (30 seconds)

**Speaker:** Jingtao

**Content:**
- Thanks to team members, instructors, course staff
- **Open for questions**

---

## EEG Q&A Preparation

**Anticipated questions:**

1. **Why only 1st-level analysis?**
   - Answer: Course requirement. Group-level statistics would be next step (t-tests on metrics across subjects).

2. **How many ICA components did you remove per subject?**
   - Answer: [State average, e.g., 2-3 components on average; mostly eye artifacts].

3. **What is the functional significance of the N1 component?**
   - Answer: N1 reflects early feature detection and selective attention in visual cortex. Enhanced N1 suggests greater neural response to salient (moving) stimuli.

4. **Did you find any significant differences in alpha power?**
   - Answer: [State results, e.g., "Yes, alpha power was suppressed more for moving condition, indicating greater attentional engagement."]

5. **How does EEG complement fMRI?**
   - Answer: fMRI provides spatial localization (V5), EEG provides temporal dynamics (when activation occurs). Together, they give a complete picture.

6. **Could you do source localization to confirm V5 activation?**
   - Answer: Yes, future direction. Would require forward/inverse modeling to map scalp EEG to cortical sources.

7. **What about other frequency bands (theta, gamma)?**
   - Answer: Focused on alpha/beta for this study. Theta often relates to memory, gamma to local processing. Could be explored in future work.

---

## Presentation Tips

### General advice for both presentations:

1. **Practice timing:**
   - Aim for 18-20 minutes to leave buffer for Q&A
   - Time each section during rehearsals

2. **Clarity:**
   - Use simple language, avoid jargon (or define it)
   - Speak slowly and clearly

3. **Visual aids:**
   - High-quality figures (large fonts, clear labels)
   - Avoid cluttered slides (one main point per slide)
   - Use color coding consistently (e.g., blue = moving, red = static)

4. **Engagement:**
   - Make eye contact with audience
   - Use pointer/laser to guide attention
   - Pause for emphasis

5. **Q&A:**
   - Listen carefully to questions
   - Repeat question if needed
   - Be honest if you don't know (offer to follow up)
   - Stay calm and confident

6. **Team coordination:**
   - Smooth transitions between speakers
   - Support each other during Q&A
   - One person manages slides, others focus on speaking

---

**Last updated:** 2025-11-24
