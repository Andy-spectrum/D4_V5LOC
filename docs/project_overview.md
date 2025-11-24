# Project Overview

## Course Information

- **Course:** fMRI & EEG Analysis Module
- **Institution:** [Your institution]
- **Semester:** Fall 2025
- **Instructors:** [Instructor names]

## Team Members

| Name | Role | Responsibilities |
|------|------|------------------|
| Dorozhkina Irina | Bachelor student | fMRI preprocessing, 1st-level GLM, visualization |
| Miloserdov Daniil | Bachelor student | fMRI 2nd-level analysis, statistical reporting |
| Jingtao Xu | Master student | EEG pipeline + fMRI support, presentations |

## Project Scope

### fMRI Component

**Dataset:** D4_V5LOC - Visual Motion Localizer

The D4_V5LOC dataset contains functional MRI data from an experiment designed to localize the visual motion processing area V5/hMT+ (middle temporal area). This region is selectively responsive to visual motion.

**Experimental paradigm:**
- Participants fixated on a central point while observing:
  - **Moving condition:** Dynamic/moving dot patterns
  - **Static condition:** Static dot patterns (baseline)
- Block design with multiple runs per subject

**Analysis objectives:**
1. Preprocess all subjects' fMRI data using SPM12
2. Perform 1st-level GLM analysis for each subject
3. Implement two 2nd-level models:
   - **Simple:** One-sample t-test on `moving > static` contrast
   - **Complex:** Flexible factorial design with block-wise comparisons

**Expected outcome:**
- Identification of V5/hMT+ activation in occipito-temporal cortex
- Statistical maps showing group-level motion processing
- Comparison of simple vs complex statistical models

### EEG Component

**Dataset:** Course EEG dataset (corresponding to visual motion paradigm)

**Analysis objectives:**
1. Perform 1st-level analysis only (per subject)
2. Implement preprocessing pipeline
3. Extract multiple signal metrics:
   - Event-Related Potentials (ERPs)
   - Time-frequency power (e.g., alpha, beta bands)
4. Compare conditions (moving vs static)

**Toolbox options:**
- MATLAB: EEGLAB or FieldTrip
- Python: MNE

**Expected outcome:**
- Clean EEG data with artifacts removed
- Condition-specific ERP waveforms
- Time-frequency maps showing oscillatory responses to motion

## Deliverables

### For fMRI Defense (15.12)

1. **Analysis results:**
   - Preprocessed data quality metrics
   - 1st-level activation maps (sample subjects)
   - 2nd-level group maps (both models)
   - Statistical tables (cluster sizes, peak coordinates, p-values)

2. **Presentation (30 minutes):**
   - Introduction to visual motion processing & V5/hMT+
   - Methods: paradigm, preprocessing, GLM design
   - Results: activation maps, statistical comparisons
   - Discussion: interpretation, limitations, conclusions

3. **Documentation:**
   - Complete analysis scripts
   - Results summary report
   - Figure panels ready for presentation

### For EEG Defense (17.12)

1. **Analysis results:**
   - Preprocessing pipeline documentation
   - ERP waveforms for all subjects
   - Time-frequency plots
   - Extracted metrics table

2. **Presentation (30 minutes):**
   - Introduction to EEG basics & visual processing
   - Methods: preprocessing steps, analysis approach
   - Results: ERP components, frequency responses
   - Discussion: neural correlates of motion perception

3. **Documentation:**
   - Complete analysis scripts
   - Results summary report
   - Figure panels ready for presentation

## Quality Standards

- **Reproducibility:** All analysis steps must be scripted and documented
- **Code quality:** Clear comments, organized structure, version control
- **Statistical rigor:** Proper multiple comparisons correction, effect sizes
- **Visualization:** Publication-quality figures with proper labels and scales
- **Documentation:** Clear explanations of methods and parameter choices

## Success Criteria

- [ ] All subjects successfully preprocessed (fMRI & EEG)
- [ ] 1st-level GLM runs without errors for all fMRI subjects
- [ ] Both 2nd-level models produce interpretable results
- [ ] V5/hMT+ activation identified and localized
- [ ] EEG preprocessing removes artifacts effectively
- [ ] EEG metrics extracted for all subjects and conditions
- [ ] Presentations completed and rehearsed
- [ ] All code committed to repository with clear documentation
- [ ] Team ready to defend both analyses on scheduled dates
