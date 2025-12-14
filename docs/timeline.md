# Project Timeline

## Overview

This document outlines the project schedule with key milestones and deliverables.

**Project duration:** ~4 weeks
**Defense dates:**
- **fMRI presentation:** December 15, 2025
- **EEG presentation:** December 17, 2025

---

## Week-by-Week Plan

### Week 1 (Nov 24 - Dec 1): Setup & Planning

#### Goals:
- Set up project repository on GitHub
- Organize data and verify access
- Install and test software (SPM, EEGLAB/FieldTrip/MNE)
- Assign team roles and responsibilities
- Read literature on V5/hMT+ and visual motion processing

#### Tasks:

**All team members:**
- [ ] Clone GitHub repository
- [ ] Read project documentation (`docs/project_overview.md`, methods files)
- [ ] Set up development environment
- [ ] Test software installations

**Irina:**
- [ ] Verify access to fMRI data (D4_V5LOC)
- [ ] Organize data into BIDS-like structure
- [ ] Document any issues or questions

**Daniil:**
- [ ] Review SPM documentation on 2nd-level designs
- [ ] Prepare analysis scripts folder structure
- [ ] Research V5/hMT+ anatomical location and expected coordinates

**Jingtao (Xu):**
- [ ] Verify access to EEG data
- [ ] Choose EEG analysis toolbox (MATLAB or Python)
- [ ] Test preprocessing pipeline on 1 subject
- [ ] Document preprocessing parameters

#### Deliverables:
- GitHub repository fully set up with docs
- Software environments ready
- Initial test runs completed

---

### Week 2 (Dec 2 - Dec 8): Data Preprocessing

#### Goals:
- Complete fMRI preprocessing for all subjects
- Complete EEG preprocessing for all subjects
- Run initial 1st-level fMRI analysis
- Document any data quality issues

#### Tasks:

**Daniil:**
- [ ] Set up 1st-level GLM batch scripts
- [ ] Test 1st-level analysis on 2-3 subjects
- [ ] Verify design matrices and contrasts are correct

**Jingtao (Xu):**
- [ ] Run EEG preprocessing pipeline on all subjects:
  - [ ] Filtering
  - [ ] ICA and artifact removal
  - [ ] Epoching and baseline correction
  - [ ] Artifact rejection
- [ ] Document preprocessing choices (filter settings, ICA components removed, etc.)
- [ ] Save cleaned epoch data for each subject
- [ ] Commit EEG preprocessing scripts to GitHub

#### Deliverables:
- All fMRI subjects preprocessed
- All EEG subjects preprocessed
- Initial 1st-level fMRI results for sample subjects
- Scripts committed and documented

#### Meeting (end of week):
- Review preprocessing results
- Discuss any data quality issues
- Plan for next week's analyses

---

### Week 3 (Dec 9 - Dec 15): Analysis & Results

#### Goals:
- Complete all fMRI 1st-level and 2nd-level analyses
- Complete EEG 1st-level analyses (ERPs & time-frequency)
- Generate figures and tables for fMRI presentation
- Prepare fMRI presentation slides

#### Tasks:

**Irina:**
- [ ] Run 1st-level GLM for all subjects (simple model)
- [ ] Verify contrast images generated correctly
- [ ] Create quality control figures (design matrices, activation maps)
- [ ] Help Daniil collect contrast images for 2nd-level

**Daniil:**
- [ ] Run 2nd-level one-sample t-test (simple model)
  - [ ] Collect all subjects' `moving > static` contrast images
  - [ ] Run group analysis in SPM
  - [ ] Generate thresholded statistical maps
  - [ ] Extract peak coordinates and statistics
- [ ] Run 2nd-level factorial model (complex model)
  - [ ] Verify block-wise contrasts from 1st-level
  - [ ] Set up flexible factorial design
  - [ ] Run model and generate results
- [ ] Create publication-quality figures:
  - [ ] Activation maps overlaid on MNI template
  - [ ] Glass brain plots
  - [ ] Tables of peak activations
- [ ] Write results section text

**Jingtao (Xu):**
- [ ] Compute ERPs for all subjects
  - [ ] Average epochs for each condition
  - [ ] Plot ERP waveforms at key electrodes (Oz, O1, O2)
  - [ ] Identify ERP components (P1, N1, P2)
- [ ] Run time-frequency analysis for all subjects
  - [ ] Compute TFRs (4-40 Hz)
  - [ ] Extract alpha and beta power
  - [ ] Create time-frequency plots
- [ ] Extract quantitative metrics:
  - [ ] ERP amplitudes (P1, N1, P2) for each subject
  - [ ] Alpha/beta power for each subject
  - [ ] Save to summary table (CSV/Excel)
- [ ] Create EEG figures for presentation
- [ ] Assist with fMRI presentation preparation

**All team members:**
- [ ] Review fMRI results together
- [ ] Prepare fMRI presentation slides
- [ ] Assign presentation sections (intro, methods, results, discussion)
- [ ] Rehearse presentation (at least twice)

#### Deliverables:
- All fMRI analyses completed
- All EEG 1st-level analyses completed
- fMRI figures and tables ready
- fMRI presentation slides completed
- **fMRI defense on Dec 15** ✅

#### Meeting (mid-week):
- Review fMRI results
- Assign presentation sections
- Practice presentation

---

### Week 4 (Dec 16 - Dec 17): EEG Presentation Preparation

#### Goals:
- Finalize EEG results and figures
- Prepare EEG presentation slides
- Rehearse EEG presentation
- Successfully defend EEG analysis on Dec 17

#### Tasks:

**Jingtao (Xu):**
- [ ] Finalize all EEG figures
- [ ] Create comparison plots (moving vs static)
- [ ] Prepare EEG presentation slides:
  - [ ] Introduction to EEG and visual processing
  - [ ] Methods: preprocessing, epoching, analysis
  - [ ] Results: ERPs, time-frequency, metrics
  - [ ] Discussion: interpretation, limitations
- [ ] Practice presenting EEG analysis

**Irina & Daniil:**
- [ ] Review EEG results
- [ ] Help with EEG presentation (introduction, discussion sections)
- [ ] Provide feedback on presentation
- [ ] Practice Q&A

**All team members:**
- [ ] Rehearse EEG presentation together
- [ ] Prepare for questions from instructors
- [ ] **EEG defense on Dec 17** 

#### Deliverables:
- EEG presentation slides completed
- EEG figures and tables finalized
- Successful EEG defense

---

## Key Milestones Summary

| Date | Milestone | Responsible |
|------|-----------|-------------|
| Dec 1 | Setup complete, test runs done | All |
| Dec 8 | All preprocessing finished | Irina, Jingtao |
| Dec 12 | All fMRI analyses complete | Irina, Daniil |
| Dec 13 | fMRI presentation ready | All |
| Dec 14 | fMRI presentation rehearsal | All |
| **Dec 15** | **fMRI Defense** | **All** |
| Dec 16 | EEG results finalized | Jingtao |
| Dec 16 (evening) | EEG presentation rehearsal | All |
| **Dec 17** | **EEG Defense** | **All** |

---

## Weekly Meeting Schedule (Suggested)

- **Day:** Every Monday, 10:00 AM
- **Duration:** 1 hour
- **Agenda:**
  - Review last week's progress
  - Discuss any issues or blockers
  - Assign tasks for the coming week
  - Coordinate on shared work

**Additional meetings as needed:**
- Ad-hoc troubleshooting sessions
- Presentation rehearsals (Week 3 & 4)

---

## Risk Management

### Potential risks and mitigation strategies:

| Risk | Impact | Mitigation |
|------|--------|------------|
| Excessive motion in fMRI data | Some subjects unusable | Flag early, discuss with instructor if exclusions needed |
| Software installation issues | Delays in analysis | Start setup in Week 1, document problems, seek help early |
| Time-consuming preprocessing | Schedule delays | Parallelize work (Irina on fMRI, Jingtao on EEG) |
| Unexpected null results | Weak presentation | Verify methods carefully, discuss alternative thresholds |
| Team member unavailability | Uneven workload | Communicate schedule conflicts early, redistribute tasks |
| Presentation nervousness | Poor defense performance | Multiple rehearsals, prepare Q&A responses |

---

## Communication & Collaboration

### Recommended tools:

- **Code & Documentation:** GitHub (commit regularly, use clear commit messages)
- **Chat/Quick questions:** [Telegram, Slack, WhatsApp group]
- **Video meetings:** [Zoom, Google Meet, Teams]
- **Shared documents:** Google Docs (for presentation slides, notes)

### Best practices:

- Commit code changes at least once per day with descriptive messages
- Update `misc/team_log.md` weekly with progress notes
- Ask questions early (don't struggle alone)
- Share preliminary results for feedback before finalizing
- Celebrate milestones (even small ones)

---

## Post-Defense (Optional)

After both defenses are complete, consider:

- [ ] Clean up code and documentation
- [ ] Write a short project summary (1-2 pages)
- [ ] Archive final results and presentations
- [ ] Reflect on what worked well and what could be improved
- [ ] Consider writing up results as a short report or blog post (good for portfolio)

---

**Last updated:** 2025-11-24
