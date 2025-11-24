# EEG Analysis Methods

## Overview

This document describes the EEG analysis pipeline for the visual motion paradigm corresponding to the D4_V5LOC dataset. The analysis focuses on **1st-level (subject-level) analyses only**, as per course requirements.

**Software options:**
- **MATLAB:** EEGLAB or FieldTrip
- **Python:** MNE-Python

**Analysis objectives:**
1. Preprocess raw EEG data (filtering, artifact removal)
2. Epoch data by experimental conditions
3. Compute Event-Related Potentials (ERPs)
4. Perform time-frequency analysis
5. Extract quantitative metrics for each subject and condition

---

## 1. Data Organization

### Expected data structure

```
eeg_data/
  sub-01/
    sub-01_task-v5loc_eeg.set          # EEGLAB format
    sub-01_task-v5loc_eeg.fdt
    sub-01_task-v5loc_events.tsv       # Event markers
  sub-02/
    ...
```

Or for other formats:
- `.bdf` or `.edf` (BioSemi)
- `.vhdr`, `.vmrk`, `.eeg` (BrainVision)
- `.fif` (Neuromag/MNE format)

### Event information

Event markers should indicate:
- **Condition:** `moving` or `static`
- **Onset time:** precise timing in samples or seconds
- **Duration:** block or trial duration

---

## 2. Software Setup

### Option A: MATLAB with EEGLAB

**Installation:**
1. Download EEGLAB from [https://sccn.ucsd.edu/eeglab/](https://sccn.ucsd.edu/eeglab/)
2. Extract to a folder (e.g., `~/MATLAB/eeglab/`)
3. In MATLAB:
   ```matlab
   addpath('/path/to/eeglab/');
   eeglab  % Launch EEGLAB GUI
   ```

**Recommended plugins:**
- **clean_rawdata** (for artifact subspace reconstruction)
- **ICLabel** (automated IC classification)
- **ERPLAB** (advanced ERP analysis)

### Option B: MATLAB with FieldTrip

**Installation:**
1. Download FieldTrip from [https://www.fieldtriptoolbox.org/](https://www.fieldtriptoolbox.org/)
2. Extract and add to MATLAB path:
   ```matlab
   addpath('/path/to/fieldtrip/');
   ft_defaults
   ```

### Option C: Python with MNE

**Installation:**
```bash
conda create -n eeg_analysis python=3.10
conda activate eeg_analysis
pip install mne numpy scipy matplotlib pandas seaborn
```

**Jupyter notebook recommended for interactive analysis:**
```bash
pip install jupyter
jupyter notebook
```

---

## 3. Preprocessing Pipeline

### 3.1 Load Raw Data

#### EEGLAB (MATLAB):
```matlab
EEG = pop_loadset('filename', 'sub-01_task-v5loc_eeg.set', 'filepath', '/path/to/data/');
eeglab redraw  % Update GUI
```

#### FieldTrip (MATLAB):
```matlab
cfg = [];
cfg.dataset = '/path/to/data/sub-01_task-v5loc_eeg.edf';
data_raw = ft_preprocessing(cfg);
```

#### MNE-Python:
```python
import mne
raw = mne.io.read_raw_edf('sub-01_task-v5loc_eeg.edf', preload=True)
```

---

### 3.2 Visual Inspection & Channel Selection

**Check:**
- Channel locations are correctly loaded
- Data looks reasonable (no flat channels, extreme noise)
- Event markers are present

**Reject bad channels** if necessary (dead or extremely noisy channels).

#### EEGLAB:
```matlab
% Plot data
pop_eegplot(EEG, 1, 1, 1);

% Remove channels interactively or by name
EEG = pop_select(EEG, 'nochannel', {'TP9', 'TP10'});  % example
```

#### MNE-Python:
```python
raw.plot(n_channels=30, scalings='auto')
raw.info['bads'] = ['TP9', 'TP10']  # mark bad channels
```

---

### 3.3 Filtering

**Goal:** Remove slow drifts and high-frequency noise.

**Recommended filter settings:**
- **High-pass filter:** 0.1 Hz (removes DC drift)
- **Low-pass filter:** 40 Hz (removes muscle artifacts and line noise above task-relevant frequencies)
- **Notch filter (optional):** 50 Hz or 60 Hz (remove power line noise)

#### EEGLAB:
```matlab
% High-pass at 0.1 Hz
EEG = pop_eegfiltnew(EEG, 'locutoff', 0.1);

% Low-pass at 40 Hz
EEG = pop_eegfiltnew(EEG, 'hicutoff', 40);

% Notch filter at 50 Hz (if needed)
EEG = pop_eegfiltnew(EEG, 'locutoff', 49, 'hicutoff', 51, 'revfilt', 1);
```

#### MNE-Python:
```python
raw.filter(l_freq=0.1, h_freq=40, fir_design='firwin')
raw.notch_filter(freqs=50)  # if needed
```

---

### 3.4 Re-referencing

**Common reference schemes:**
- **Average reference:** use mean of all electrodes (most common for dense arrays)
- **Mastoid reference:** average of TP9 & TP10 or M1 & M2
- **Linked ears:** if recorded with ear/mastoid electrodes

#### EEGLAB:
```matlab
% Average reference
EEG = pop_reref(EEG, []);
```

#### MNE-Python:
```python
raw.set_eeg_reference('average', projection=True)
```

---

### 3.5 Artifact Removal: Independent Component Analysis (ICA)

**Purpose:** Identify and remove stereotypical artifacts (eye blinks, eye movements, muscle activity).

**Steps:**
1. Run ICA on filtered data
2. Identify artifact components (manual or automated)
3. Remove artifact components

#### EEGLAB:
```matlab
% Run ICA (this can take several minutes)
EEG = pop_runica(EEG, 'icatype', 'runica', 'extended', 1);

% Visualize components
pop_selectcomps(EEG, 1:20);  % inspect first 20 ICs

% Or use ICLabel for automated classification
EEG = iclabel(EEG);
EEG = pop_icflag(EEG, [NaN NaN; 0.9 1; 0.9 1; NaN NaN; NaN NaN; NaN NaN; NaN NaN]);
% This flags components with >90% probability of being eye or muscle artifact

% Remove flagged components
EEG = pop_subcomp(EEG, [], 0);
```

#### MNE-Python:
```python
from mne.preprocessing import ICA

# Fit ICA (can take several minutes)
ica = ICA(n_components=20, random_state=42)
ica.fit(raw)

# Plot components
ica.plot_components()

# Identify eye blink components using EOG channel (if available)
eog_indices, eog_scores = ica.find_bads_eog(raw, ch_name='EOG')
ica.exclude = eog_indices

# Apply ICA (remove components)
raw_clean = ica.apply(raw.copy())
```

---

### 3.6 Epoch Extraction

**Purpose:** Segment continuous data into trials/blocks based on event markers.

**Parameters:**
- **Epoch window:** e.g., -0.5 to 2.0 seconds relative to block onset
- **Baseline period:** e.g., -0.2 to 0 seconds (used for baseline correction)

#### EEGLAB:
```matlab
% Extract epochs for 'moving' condition
EEG_moving = pop_epoch(EEG, {'moving'}, [-0.5 2.0], 'epochinfo', 'yes');

% Extract epochs for 'static' condition
EEG_static = pop_epoch(EEG, {'static'}, [-0.5 2.0], 'epochinfo', 'yes');

% Baseline correction
EEG_moving = pop_rmbase(EEG_moving, [-200 0]);  % baseline from -200 to 0 ms
EEG_static = pop_rmbase(EEG_static, [-200 0]);
```

#### MNE-Python:
```python
# Create events array
events = mne.find_events(raw, stim_channel='STI 014')

# Define event IDs
event_id = {'moving': 1, 'static': 2}

# Epoch data
epochs = mne.Epochs(raw_clean, events, event_id, tmin=-0.5, tmax=2.0,
                    baseline=(-0.2, 0), preload=True)

# Separate conditions
epochs_moving = epochs['moving']
epochs_static = epochs['static']
```

---

### 3.7 Artifact Rejection (Epoch-Level)

**Purpose:** Remove epochs with remaining artifacts (large amplitude, drift).

**Thresholds:**
- Amplitude > ±100 μV (can adjust based on data)
- Improbable data (kurtosis test)

#### EEGLAB:
```matlab
% Reject epochs by amplitude
EEG_moving = pop_eegthresh(EEG_moving, 1, 1:EEG_moving.nbchan, -100, 100, -0.5, 2.0, 0, 1);
EEG_static = pop_eegthresh(EEG_static, 1, 1:EEG_static.nbchan, -100, 100, -0.5, 2.0, 0, 1);
```

#### MNE-Python:
```python
# Reject by peak-to-peak amplitude
reject_criteria = dict(eeg=100e-6)  # 100 µV
epochs.drop_bad(reject=reject_criteria)
```

---

## 4. Event-Related Potential (ERP) Analysis

### 4.1 Compute ERPs

**ERP = average of all epochs for a given condition.**

#### EEGLAB:
```matlab
% Compute ERP for moving condition
ERP_moving = mean(EEG_moving.data, 3);  % average across 3rd dimension (trials)

% Compute ERP for static condition
ERP_static = mean(EEG_static.data, 3);
```

Or use **ERPLAB** plugin for advanced ERP analysis:
```matlab
ERP_moving = pop_averager(EEG_moving);
ERP_static = pop_averager(EEG_static);
```

#### MNE-Python:
```python
evoked_moving = epochs_moving.average()
evoked_static = epochs_static.average()

# Plot
evoked_moving.plot(spatial_colors=True)
evoked_static.plot(spatial_colors=True)
```

---

### 4.2 Visualize ERPs

**Goal:** Plot ERP waveforms for key electrodes (e.g., occipital channels for visual response).

#### EEGLAB/ERPLAB:
```matlab
% Plot ERPs at Oz electrode
figure;
plot(EEG_moving.times, ERP_moving(find(strcmp({EEG_moving.chanlocs.labels}, 'Oz')), :), 'b', 'LineWidth', 2);
hold on;
plot(EEG_static.times, ERP_static(find(strcmp({EEG_static.chanlocs.labels}, 'Oz')), :), 'r', 'LineWidth', 2);
xlabel('Time (ms)'); ylabel('Amplitude (μV)');
legend({'Moving', 'Static'});
title('ERP at Oz');
```

#### MNE-Python:
```python
# Compare conditions
mne.viz.plot_compare_evokeds({'Moving': evoked_moving, 'Static': evoked_static},
                             picks='Oz', show_sensors=True)
```

---

### 4.3 Statistical Comparison of ERPs

**Goal:** Test if ERP amplitudes differ between conditions.

**Approach:**
- Define a time window of interest (e.g., 150-250 ms for P200 component)
- Extract mean amplitude in this window for each subject and condition
- Perform paired t-test across subjects (at 2nd-level, if doing group analysis)

#### Example (for single subject, 1st-level):
```matlab
% Define time window
time_window = [150 250];  % ms
time_idx = find(EEG_moving.times >= time_window(1) & EEG_moving.times <= time_window(2));

% Extract mean amplitude at Oz
amp_moving = mean(ERP_moving(find(strcmp({EEG_moving.chanlocs.labels}, 'Oz'}), time_idx));
amp_static = mean(ERP_static(find(strcmp({EEG_static.chanlocs.labels}, 'Oz'}), time_idx));

fprintf('Moving: %.2f μV, Static: %.2f μV\n', amp_moving, amp_static);
```

For group-level analysis, collect these values from all subjects and run statistical tests in MATLAB, R, or Python.

---

## 5. Time-Frequency Analysis

### 5.1 Compute Time-Frequency Representations (TFRs)

**Purpose:** Examine oscillatory power (e.g., alpha, beta) as a function of time and frequency.

**Methods:**
- **Short-time Fourier Transform (STFT)** or **Wavelet convolution**
- Frequency range: 4-40 Hz (theta to gamma)
- Time window: full epoch

#### EEGLAB:
```matlab
% Using EEGLAB's newtimef function
figure;
pop_newtimef(EEG_moving, 1, find(strcmp({EEG_moving.chanlocs.labels}, 'Oz')), ...
             [-500 2000], [3 0.5], 'topovec', 1, 'elocs', EEG_moving.chanlocs, ...
             'chaninfo', EEG_moving.chaninfo, 'baseline', [-200 0], 'freqs', [4 40]);
title('Time-Frequency: Moving condition at Oz');
```

#### FieldTrip:
```matlab
% Define parameters
cfg = [];
cfg.method = 'mtmconvol';
cfg.foi = 4:2:40;  % frequencies of interest
cfg.t_ftimwin = ones(size(cfg.foi)) * 0.5;  % window length
cfg.toi = -0.5:0.05:2.0;  % time points
cfg.taper = 'hanning';

% Compute TFR
TFR_moving = ft_freqanalysis(cfg, epochs_moving_ft);
TFR_static = ft_freqanalysis(cfg, epochs_static_ft);
```

#### MNE-Python:
```python
# Compute time-frequency using Morlet wavelets
freqs = np.arange(4, 40, 2)
n_cycles = freqs / 2.0  # variable number of cycles

power_moving = mne.time_frequency.tfr_morlet(epochs_moving, freqs=freqs, n_cycles=n_cycles,
                                              return_itc=False, average=True)
power_static = mne.time_frequency.tfr_morlet(epochs_static, freqs=freqs, n_cycles=n_cycles,
                                              return_itc=False, average=True)

# Plot
power_moving.plot([power_moving.ch_names.index('Oz')], baseline=(-0.2, 0), mode='logratio', title='Moving')
power_static.plot([power_static.ch_names.index('Oz')], baseline=(-0.2, 0), mode='logratio', title='Static')
```

---

### 5.2 Extract Time-Frequency Metrics

**Goal:** Quantify power in specific frequency bands and time windows.

**Example:**
- **Alpha band (8-12 Hz):** often suppressed during visual attention
- **Beta band (15-25 Hz):** related to motor and sensory processing

#### Extract alpha power in 0-500 ms window:
```python
# In MNE-Python
alpha_freqs = (8, 12)
time_window = (0, 0.5)

# Crop power to frequency and time of interest
power_alpha_moving = power_moving.copy().crop(tmin=time_window[0], tmax=time_window[1],
                                               fmin=alpha_freqs[0], fmax=alpha_freqs[1])
power_alpha_static = power_static.copy().crop(tmin=time_window[0], tmax=time_window[1],
                                               fmin=alpha_freqs[0], fmax=alpha_freqs[1])

# Compute mean power
mean_alpha_moving = power_alpha_moving.data.mean()
mean_alpha_static = power_alpha_static.data.mean()

print(f"Alpha power - Moving: {mean_alpha_moving:.2e}, Static: {mean_alpha_static:.2e}")
```

---

## 6. Extracting Quantitative Metrics for All Subjects

For each subject, extract and save the following metrics to a summary table:

### ERP Metrics:
- **P1 amplitude** (peak ~100 ms at occipital electrodes)
- **N1 amplitude** (peak ~150-200 ms)
- **P2 amplitude** (peak ~200-300 ms)
- **Mean amplitude** in predefined time windows
- **Peak latency** (time of maximum amplitude)

### Time-Frequency Metrics:
- **Alpha power** (8-12 Hz, averaged over time window)
- **Beta power** (15-25 Hz)
- **Theta power** (4-7 Hz, if relevant)

### Output Format (CSV or Excel):

| Subject | Condition | Electrode | P1_amplitude | N1_amplitude | Alpha_power | Beta_power |
|---------|-----------|-----------|--------------|--------------|-------------|------------|
| sub-01  | moving    | Oz        | 4.5          | -3.2         | 0.012       | 0.008      |
| sub-01  | static    | Oz        | 3.8          | -2.7         | 0.015       | 0.009      |
| sub-02  | moving    | Oz        | 5.1          | -4.0         | 0.011       | 0.007      |
| ...     | ...       | ...       | ...          | ...          | ...         | ...        |

---

## 7. Visualization for Presentation

### Recommended figures:

1. **Grand average ERP waveforms** (moving vs static, overlay at key electrodes)
2. **Topographic maps** of ERP at specific time points (e.g., P1, N1 peaks)
3. **Time-frequency plots** (spectrograms) for each condition
4. **Bar plots** comparing metrics (e.g., mean alpha power: moving vs static)

#### Example: ERP topographic maps (MNE-Python)
```python
# Plot topography at 150 ms
evoked_moving.plot_topomap(times=[0.15], ch_type='eeg', show_names=True)
```

#### Example: Difference plot (moving - static)
```python
evoked_diff = mne.combine_evoked([evoked_moving, evoked_static], weights=[1, -1])
evoked_diff.plot_joint(times=[0.1, 0.15, 0.2, 0.3])
```

---

## 8. Quality Control Checklist

- [ ] Raw data loaded successfully for all subjects
- [ ] Bad channels identified and removed
- [ ] Filtering applied appropriately (high-pass, low-pass)
- [ ] ICA performed and artifact components removed
- [ ] Epochs extracted and baseline-corrected
- [ ] Artifact rejection completed (bad epochs removed)
- [ ] ERPs computed and visually inspected
- [ ] Time-frequency analysis completed
- [ ] Metrics extracted and saved to summary table
- [ ] Figures prepared for presentation

---

## 9. Troubleshooting

**Issue:** ICA does not converge or takes very long
- Solution: Reduce data rank (remove more channels or downsample), use extended infomax algorithm

**Issue:** Too many epochs rejected
- Solution: Adjust rejection threshold, check preprocessing steps (filtering, ICA)

**Issue:** ERPs are very noisy
- Solution: Check number of trials averaged (need at least 20-30 clean trials per condition), verify baseline correction

**Issue:** No clear ERP components visible
- Solution: Check electrode locations, verify event markers are correct, ensure sufficient SNR in data

---

## 10. References

- Luck, S. J. (2014). *An Introduction to the Event-Related Potential Technique* (2nd ed.). MIT Press.
- Cohen, M. X. (2014). *Analyzing Neural Time Series Data: Theory and Practice*. MIT Press.
- Makeig, S., et al. (1996). Independent component analysis of electroencephalographic data. *Advances in Neural Information Processing Systems*, 8, 145-151.

**EEGLAB Tutorial:** [https://eeglab.org/tutorials/](https://eeglab.org/tutorials/)
**FieldTrip Tutorial:** [https://www.fieldtriptoolbox.org/tutorial/](https://www.fieldtriptoolbox.org/tutorial/)
**MNE-Python Tutorial:** [https://mne.tools/stable/auto_tutorials/index.html](https://mne.tools/stable/auto_tutorials/index.html)
