# EEG Data Directory

## Overview

This directory should contain the raw and preprocessed EEG data for the visual motion paradigm corresponding to the D4_V5LOC dataset.

**IMPORTANT:** Raw EEG files can be large (hundreds of MB per subject). Consider selective commits to GitHub or store locally only.

---

## Expected Data Structure

### Raw data (before preprocessing):

```
eeg/data/
├── sub-01/
│   ├── sub-01_task-v5loc_eeg.set        # EEGLAB format (or .bdf, .vhdr, .fif)
│   ├── sub-01_task-v5loc_eeg.fdt        # Associated data file (if .set format)
│   └── sub-01_task-v5loc_events.tsv     # Event markers
├── sub-02/
│   ├── sub-02_task-v5loc_eeg.set
│   ├── sub-02_task-v5loc_eeg.fdt
│   └── sub-02_task-v5loc_events.tsv
├── sub-03/
│   └── ...
└── README_data.md (this file)
```

### Preprocessed data:

After running the preprocessing pipeline, save cleaned data with descriptive names:

```
eeg/data/sub-01/
├── sub-01_task-v5loc_eeg.set               # Raw
├── sub-01_task-v5loc_eeg_preprocessed.set  # After filtering, ICA, artifact removal
├── sub-01_task-v5loc_eeg_epochs.set        # Epoched data (moving + static trials)
└── sub-01_task-v5loc_eeg_ica_components.mat # Saved ICA weights (optional)
```

Or if using Python/MNE:
```
eeg/data/sub-01/
├── sub-01_task-v5loc_raw.fif               # Raw data in MNE format
├── sub-01_task-v5loc_preprocessed-raw.fif  # After filtering & ICA
└── sub-01_task-v5loc_epo.fif               # Epoched data
```

---

## EEG Acquisition Details

**System:** [e.g., BioSemi ActiveTwo, BrainProducts actiCHamp, Neuromag]

**Recording parameters:**
- Number of channels: [e.g., 32 or 64]
- Sampling rate: [e.g., 500 Hz or 1000 Hz]
- Reference: [e.g., CMS/DRL (BioSemi), FCz, or linked mastoids]
- Ground: [e.g., driven right leg (DRL)]
- Electrode type: [e.g., Active Ag/AgCl electrodes]
- Electrode cap: [e.g., 10-20 system, custom layout]

**Additional channels (if available):**
- EOG (electrooculography): for eye movement tracking
- ECG (electrocardiography): for heart rate monitoring (artifact removal)
- External triggers: for event synchronization

**Impedances:**
- Target: < 5 kΩ (BioSemi active electrodes: typically < 50 kΩ acceptable)

---

## Event Markers

Event markers should indicate the onset of each condition block or trial.

**Event types:**
- `moving`: onset of moving dots block
- `static`: onset of static dots block

### events.tsv format:

| onset | duration | trial_type |
|-------|----------|------------|
| 5.0   | 20.0     | moving     |
| 25.0  | 20.0     | static     |
| 45.0  | 20.0     | moving     |
| ...   | ...      | ...        |

- **onset:** Time in seconds from recording start
- **duration:** Block/trial duration in seconds
- **trial_type:** Condition label

### Event codes (if using numeric triggers):

Example event code mapping:
- `1`: moving condition
- `2`: static condition
- `99`: experiment start/end

---

## Data Access

**Where to get the data:**
- [Specify data source, e.g., course server, shared folder]
- Path: [e.g., `/course_data/EEG_V5LOC/`]

**Downloading data:**
```bash
# Example
scp -r username@server:/path/to/EEG_V5LOC/sub-* eeg/data/
```

---

## Data Formats

### Common EEG file formats:

1. **EEGLAB (.set + .fdt):**
   - MATLAB-based, widely used
   - `.set` = header/metadata (small)
   - `.fdt` = actual data (large)

2. **BrainVision (.vhdr + .vmrk + .eeg):**
   - `.vhdr` = header
   - `.vmrk` = markers/events
   - `.eeg` = data

3. **BioSemi (.bdf):**
   - Native format for BioSemi systems
   - 24-bit resolution

4. **Neuromag/MNE (.fif):**
   - Used by MNE-Python
   - Can store raw, preprocessed, and epoched data

5. **European Data Format (.edf):**
   - Standard format, widely compatible

---

## Preprocessing Recommendations

See `docs/methods_eeg.md` for detailed preprocessing steps.

**Summary:**
1. Load raw data
2. Filter: 0.1-40 Hz (high-pass + low-pass), optional 50/60 Hz notch
3. Re-reference: average reference or mastoid
4. ICA: identify and remove eye artifacts, muscle noise
5. Epoch: segment into trials/blocks
6. Baseline correction: -200 to 0 ms
7. Artifact rejection: amplitude threshold (±100 μV)

---

## Data Quality Checks

### Before preprocessing:
- [ ] All channels present and labeled correctly
- [ ] Event markers detected and aligned with experiment log
- [ ] No flat or disconnected channels
- [ ] Impedances within acceptable range (check lab notes)

### After preprocessing:
- [ ] Filtering applied successfully (check power spectrum)
- [ ] ICA components reviewed (eye artifacts identified)
- [ ] Epochs extracted correctly (check trial counts)
- [ ] No excessive epoch rejection (> 30% rejected → investigate)

---

## Example: Loading Data in EEGLAB

```matlab
% Launch EEGLAB
eeglab

% Load raw data
EEG = pop_loadset('filename', 'sub-01_task-v5loc_eeg.set', 'filepath', 'eeg/data/sub-01/');

% Check dataset info
EEG.nbchan   % Number of channels
EEG.trials   % Number of epochs (if epoched)
EEG.pnts     % Number of time points per epoch
EEG.srate    % Sampling rate
EEG.event    % Event structure

% Plot raw data
pop_eegplot(EEG, 1, 1, 1);
```

---

## Example: Loading Data in MNE-Python

```python
import mne

# Load raw data
raw = mne.io.read_raw_edf('eeg/data/sub-01/sub-01_task-v5loc_eeg.edf', preload=True)

# Check dataset info
print(raw.info)

# Plot raw data
raw.plot(n_channels=30, scalings='auto')

# Extract events
events = mne.find_events(raw, stim_channel='STI 014')
print(events)
```

---

## Backup & Version Control

**Backup strategy:**
- Keep raw data on secure storage (lab server, external drive)
- Preprocessed data can be regenerated from raw data + scripts

**Git considerations:**
- **Do NOT commit large data files** (`.fdt`, `.fif` data files > 100 MB)
- **DO commit:**
  - `.set` files (if small, < 1 MB)
  - Event files (`.tsv`)
  - Metadata and documentation

Add to `.gitignore`:
```
*.fdt
*.fif
*-raw.fif
*_epo.fif
*.bdf
*.eeg
```

---

## Common Issues & Solutions

| Issue | Possible Cause | Solution |
|-------|---------------|----------|
| Event markers missing | Trigger channel not recorded | Check original recording setup; manually add events if log available |
| High impedance channels | Poor electrode contact | Check lab notes; consider excluding bad channels |
| Excessive 50/60 Hz noise | Power line interference | Apply notch filter during preprocessing |
| Drift in signal | DC offset, slow drift | Apply high-pass filter (0.1 Hz or 0.5 Hz) |
| Eye blink artifacts | Not removed by ICA | Re-run ICA with more components; manually reject epochs |

---

## Contact

For questions about EEG data or equipment:
- **Data curator:** [Name, email]
- **Lab manager:** [Name, email]
- **Course instructor:** [Name, email]

---

**Last updated:** 2025-11-24
