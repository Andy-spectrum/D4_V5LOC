# fMRI Data Directory

## Overview

This directory should contain the raw and preprocessed fMRI data for the D4_V5LOC dataset.

**IMPORTANT:** Raw fMRI data files are typically very large (several GB per subject) and should **NOT** be committed to GitHub. Use this directory for local storage only.

---

## Expected Data Structure

### Raw data (before preprocessing):

```
fmri/data/
├── sub-01/
│   ├── anat/
│   │   └── sub-01_T1w.nii            # Structural T1-weighted image
│   └── func/
│       ├── sub-01_task-v5loc_bold.nii   # Functional BOLD images
│       └── sub-01_task-v5loc_events.tsv # Event timing information
├── sub-02/
│   ├── anat/
│   │   └── sub-02_T1w.nii
│   └── func/
│       ├── sub-02_task-v5loc_bold.nii
│       └── sub-02_task-v5loc_events.tsv
├── sub-03/
│   └── ...
└── README_data.md (this file)
```

### Preprocessed data (after SPM pipeline):

Preprocessed images will be saved in the subject's functional directory with prefixes indicating the processing steps:

- `a` = slice-time corrected
- `r` = realigned (motion corrected)
- `w` = normalized (warped to MNI space)
- `s` = smoothed

Example:
```
fmri/data/sub-01/func/
├── sub-01_task-v5loc_bold.nii           # Original
├── rsub-01_task-v5loc_bold.nii          # Realigned
├── wrsub-01_task-v5loc_bold.nii         # Normalized
├── swrsub-01_task-v5loc_bold.nii        # Smoothed (final preprocessed)
├── rp_sub-01_task-v5loc_bold.txt        # Motion parameters (6 columns)
└── meansub-01_task-v5loc_bold.nii       # Mean functional image
```

---

## Data Acquisition Details

**Scanner:** [e.g., 3T Siemens Prisma]

**Functional (BOLD) imaging parameters:**
- Sequence: EPI (Echo-Planar Imaging)
- TR (Repetition Time): [e.g., 2.0 seconds]
- TE (Echo Time): [e.g., 30 ms]
- Flip angle: [e.g., 90°]
- Voxel size: [e.g., 3 × 3 × 3 mm³]
- Matrix size: [e.g., 64 × 64]
- Number of slices: [e.g., 35]
- Slice thickness: [e.g., 3 mm]
- Number of volumes: [e.g., 240 per run]

**Anatomical (T1-weighted) imaging parameters:**
- Sequence: MPRAGE
- TR: [e.g., 2.3 seconds]
- TE: [e.g., 2.98 ms]
- Flip angle: [e.g., 9°]
- Voxel size: [e.g., 1 × 1 × 1 mm³]
- Matrix size: [e.g., 256 × 256]

---

## Event Timing Format

The `events.tsv` file should contain three columns (tab-separated):

| onset | duration | trial_type |
|-------|----------|------------|
| 0.0   | 20.0     | moving     |
| 20.0  | 20.0     | static     |
| 40.0  | 20.0     | moving     |
| 60.0  | 20.0     | static     |
| ...   | ...      | ...        |

- **onset:** Start time of the block in seconds (relative to scan start)
- **duration:** Length of the block in seconds
- **trial_type:** Condition name (`moving` or `static`)

---

## Data Access

**Where to get the data:**
- [Specify data source, e.g., course server, shared drive, instructor]
- Path: [e.g., `/course_data/D4_V5LOC/`]

**Downloading data:**
```bash
# Example command (adjust based on actual data source)
scp -r username@server:/path/to/D4_V5LOC/sub-* fmri/data/
```

---

## Data Quality Notes

### Motion parameters:

After preprocessing, check motion parameters for each subject:

```matlab
% In MATLAB/SPM:
motion_params = load('fmri/data/sub-01/func/rp_sub-01_task-v5loc_bold.txt');
figure;
plot(motion_params);
legend({'x trans', 'y trans', 'z trans', 'x rot', 'y rot', 'z rot'});
xlabel('Volume');
ylabel('Movement (mm or radians)');
title('Motion parameters - sub-01');
```

**Quality threshold:**
- Translation: < 3 mm
- Rotation: < 3 degrees (0.052 radians)

If a subject exceeds these thresholds, consider excluding or applying more aggressive motion correction.

---

## Backup & Version Control

**Backup strategy:**
- Keep a copy of raw data in a separate location (external drive, lab server)
- Preprocessed data can be regenerated from raw data + scripts

**Do NOT commit to GitHub:**
- `.nii` or `.nii.gz` files (use `.gitignore`)
- Large data files (> 100 MB)

**What to commit:**
- Event timing files (`.tsv`) - small text files
- Documentation (this README)
- Metadata or summary files

---

## Contact

For questions about data access or quality issues, contact:
- **Data curator:** [Name, email]
- **Course instructor:** [Name, email]

---

**Last updated:** 2025-11-24
