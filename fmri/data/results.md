# Subject-Level Results

### Figure: Design matrix and design orthogonality for a single subject (first-level GLM): sub-009

![Design matrix: sub-009](https://github.com/Andy-spectrum/D4_V5LOC/blob/main/fmri/data/figures/desmat_001.png?raw=true)

This figure shows the first-level design matrix used for single-subject analysis in SPM.
The matrix includes task-related regressors (convolved with the canonical HRF), temporal derivatives, nuisance regressors (six head motion parameters), and a constant term.

The lower panel visualizes design orthogonality, quantified as the absolute cosine of the angle between columns of the design matrix.
White values indicate orthogonal regressors, black values indicate collinearity, and intermediate gray values indicate partial correlation.

Overall, the design demonstrates acceptable orthogonality between task regressors and nuisance covariates, supporting stable parameter estimation in the GLM.

 
### Figure: Statistical parametric map (SPM{T}) for the moving dots contrast (single subject)


![Sections: sub-009](https://github.com/Andy-spectrum/D4_V5LOC/blob/main/fmri/data/figures/sect_001.png?raw=true)

This figure presents voxel-wise T-statistics for the moving dots condition at the first level.
Statistical maps are displayed on sagittal, coronal, and axial anatomical slices.

The contrast tests task-related activation against baseline using a one-sided T-test.
Results are thresholded at p < 0.05, FWE-corrected at the voxel level, with no extent threshold applied.

Significant activations are observed primarily in posterior visual and motion-sensitive cortical regions, consistent with expected responses to visual motion stimuli.

### Figure: Statistical table of peak- and cluster-level results (first-level): only first table page included

![Table: sub-009](https://github.com/Andy-spectrum/D4_V5LOC/blob/main/fmri/data/figures/wholebrain_001.png?raw=true)


# Group-Level Results

## T-test 

### Figure: Statistical table of peak- and cluster-level results (second-level):  only first table page included

![Table: T-test](https://github.com/Andy-spectrum/D4_V5LOC/blob/main/fmri/data/figures/ttest_table_001.png?raw=true)


For a one-sample T-test there is only one parameter to estimate: the group mean. This is why the design matrix appears as a single “square” (one column). The second-level analysis uses a one-sample T-test across 10 subjects. Therefore, the residual degrees of freedom equal N − 1 = 9, reflecting estimation of the group mean from the data.

### Figure: Second-level T-test: Statistical Parametric Map (SPM{T})

![Sections: T-test](https://github.com/Andy-spectrum/D4_V5LOC/blob/main/fmri/data/figures/ttest_sect_001.png?raw=true)

This figure shows the results of a second-level (group-level) one-sample T-test performed in SPM. The statistical map represents voxels where the group-mean contrast value significantly differs from zero across subjects. Results are displayed on sagittal, coronal, and axial slices of the normalized anatomical template. The analysis uses an uncorrected voxel-wise threshold (e.g. p < 0.0005, uncorrected), colored clusters indicate voxels with positive T-values, the color bar represents T-statistic magnitude, crosshairs mark the current local maximum.

## Flexible Factorial 

### Figure: Design matrix and design orthogonality for a FF model

![Design matrix: FF](https://github.com/Andy-spectrum/D4_V5LOC/blob/main/fmri/data/figures/flex-design_001.png?raw=true)

Visualization of the design matrix orthogonality for the second-level flexible factorial model.
The matrix shows pairwise correlations between regressors corresponding to experimental conditions and subject effects.
White cells indicate orthogonal regressors, black cells indicate collinearity, and gray cells indicate partial correlation.
The structure confirms adequate separation between condition and subject regressors.

### Figure: Second-level Flexible Factorial: Statistical Parametric Map (SPM{T})

![Sections: FF](https://github.com/Andy-spectrum/D4_V5LOC/blob/main/fmri/data/figures/flex-sect_001.png?raw=true)

Statistical parametric maps (SPM{T}) from the flexible factorial second-level analysis.
Results are displayed on sagittal, coronal, and axial slices.
The maps show brain regions exhibiting a significant effect for the specified T-contrast after FWE correction.
Crosshairs indicate the peak voxel within the selected cluster.

### Figure: Statistical results table (flexible factorial analysis)

![Sections: FF](https://github.com/Andy-spectrum/D4_V5LOC/blob/main/fmri/data/figures/flex-table_001.png?raw=true)

Summary of voxel-level, cluster-level, and set-level statistics for the flexible factorial T-contrast.
P-values are corrected for multiple comparisons using Random Field Theory (FWE).
Coordinates are reported in MNI space, and only local maxima separated by at least 8 mm are listed.


## Full Factorial 

### Figure: Design orthogonality matrix for the second-level full factorial model

![Design matrix: FuFa](https://github.com/Andy-spectrum/D4_V5LOC/blob/main/fmri/data/figures/fufa-design_001.png?raw=true)

The figure shows the correlation structure between regressors corresponding to experimental conditions and subject effects, demonstrating that the design is well-conditioned with minimal collinearity.

### Figure: Statistical parametric maps (SPM{T}) for a second-level full factorial analysis

![Sections: FuFa](https://github.com/Andy-spectrum/D4_V5LOC/blob/main/fmri/data/figures/fufa-sect_001.png?raw=true)

Results are shown as orthogonal brain sections highlighting voxels with significant effects for the specified t-contrast, thresholded using family-wise error (FWE) correction.

### Figure: Tabulated second-level full factorial results with p-values corrected for search volume


![Table: FuFa](https://github.com/Andy-spectrum/D4_V5LOC/blob/main/fmri/data/figures/fufa-table_001.png?raw=true)

The table reports set-level, cluster-level, and peak-level statistics, including T-values, Z-scores, and MNI coordinates for significant activations.
