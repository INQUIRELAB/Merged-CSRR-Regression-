# README — Regression Dataset for the Dual-Band THz Metamaterial Absorber Study

## 1. Overview
This file describes the dataset in `regression_dataset.xlsx`, which was used for the machine-learning study reported in the accompanying paper on a tunable dual-band terahertz (THz) metamaterial absorber.

The dataset links **geometric design parameters** of the absorber to its **two resonance locations** and **their absorption amplitudes**. In the paper, this dataset supports both:

- **Forward design:** predict spectral response from geometry
- **Inverse design:** estimate geometry from desired spectral targets

The dataset was built from full-wave electromagnetic simulations of the absorber geometry, and the forward learning targets are the two resonance descriptors and their amplitudes.

---


**Paper title:** *Tunable Dual-Band THz Metamaterial Absorber with Regression-Learning-Enabled Numerical Redesign*

The paper states that the dataset consists of geometric parameters as inputs and the corresponding resonance/frequency and absorption information as outputs.

---

## 3. File contents
**Workbook:** `regression_dataset.xlsx`  
**Worksheet:** `result_navigator`

The worksheet contains **10 columns**:

1. `3D Run ID`
2. `circlein`
3. `circleout`
4. `cut`
5. `smallcirclein`
6. `smallcircleout`
7. `wavelength (1st_freq)`
8. `wavelength (2nd_freq)`
9. `Amplitude(1st freq), %`
10. `Amplitude(2nd freq)`

---

## 4. Column meanings
### Identifier
- **`3D Run ID`**: Unique simulation/sample identifier.

### Geometric inputs
These columns describe the resonator geometry used in each electromagnetic simulation.

- **`circlein`**: Inner radius of the larger circular resonator  
- **`circleout`**: Outer radius of the larger circular resonator  
- **`cut`**: Split-gap length  
- **`smallcirclein`**: Inner radius of the smaller circular resonator  
- **`smallcircleout`**: Outer radius of the smaller circular resonator

### Spectral outputs
- **`wavelength (1st_freq)`**: Resonance location corresponding to the first absorption peak  
- **`wavelength (2nd_freq)`**: Resonance location corresponding to the second absorption peak  
- **`Amplitude(1st freq), %`**: Absorption amplitude at the first resonance peak  
- **`Amplitude(2nd freq)`**: Absorption amplitude at the second resonance peak

---

## 5. Mapping to paper notation
The paper mainly describes the geometry using the symbols below:

- **I** → inner radius of the larger ring
- **O** → outer radius of the larger ring
- **IS** → inner radius of the smaller ring
- **IO** → outer radius of the smaller ring
- **S** → split-gap length

In the spreadsheet, the corresponding columns are:

| Spreadsheet column | Paper notation | Meaning |
|---|---|---|
| `circlein` | I | Inner radius of larger ring |
| `circleout` | O | Outer radius of larger ring |
| `smallcirclein` | IS | Inner radius of smaller ring |
| `smallcircleout` | IO | Outer radius of smaller ring |
| `cut` | S | Split-gap length |

The paper also describes the learning targets as:

- **f1**: first resonance
- **f2**: second resonance
- **A1**: absorption amplitude at first resonance
- **A2**: absorption amplitude at second resonance

In the spreadsheet, the two resonance-location columns are stored as **wavelength-style output columns** (`wavelength (1st_freq)` and `wavelength (2nd_freq)`), while the paper mostly discusses them as resonance frequencies. Users should therefore verify the exact unit convention used in their simulation/export workflow before direct reuse in new models.

---

## 6. Units and practical interpretation
Based on the values in the spreadsheet and the dimensions reported in the paper, the geometry columns appear to be stored in a scaled metric form consistent with the absorber dimensions reported in micrometers. For example:

- `circleout = 0.0155` corresponds to about **15.5 µm**
- `cut = 0.0012` corresponds to about **1.2 µm**
- `smallcircleout = 0.0040` corresponds to about **4.0 µm**

This means the geometric values are stored in **millimeters** (mm).

The resonance-location columns are labeled as wavelengths. Example values such as `207360` align with the first THz resonance reported in the paper when interpreted as approximately **207.36 µm** (or equivalently **207360 nm**). Because of this, users should check unit consistency carefully before training or comparing models.

**Recommended practice before reuse:**
- convert all geometric inputs to a single explicit unit system (for example µm), and
- convert resonance outputs to either wavelength or frequency consistently, depending on the intended ML task.

---

## 7. Dataset size and validity notes
The paper explains that many parameter combinations were generated, but only **geometrically valid** configurations were retained, especially those satisfying the physical constraint:

- **`smallcircleout >= smallcirclein`**

The paper reports:
- initial parameter sweep over the design variables,
- removal of non-physical geometries,
- final retention of **373 realizable samples** for learning.

### What appears in the spreadsheet
The workbook contains:
- **374 non-empty rows including one placeholder/blank record**, and
- **373 simulation IDs (1 to 373)** plus one trailing empty row with run ID `0`.


### 8. Suggested cleaned version for ML use
For model training, it is safest to:
1. remove the blank trailing row,
2. remove or impute incomplete rows with missing targets,
3. verify all geometry rows satisfy the physical validity constraint,
4. standardize units before training.

This yields a clean subset suitable for regression-based forward or inverse modeling.

---


---

## 9. Intended machine-learning usage
This dataset was used in the paper for two complementary tasks.

### Forward design
**Input:** geometry  
**Output:** resonance response

Typical mapping:
- `[I, IS, IO, S] -> [f1, f2, A1, A2]`

### Inverse design
**Input:** desired resonance response  
**Output:** geometry estimate

Typical mapping:
- `[f1, f2, A1, A2] -> [I, IS, IO, S]`

The paper evaluates multiple regression models for these tasks, including:
- Linear Regression
- Lasso Regression
- Decision Tree Regression
- Random Forest Regression
- Gradient Boosting Regression

---

## 10. Recommended preprocessing before reuse
Before using this dataset in a new study, it is recommended to:

1. **Rename columns clearly**
   - `circlein` -> `I_large_inner`
   - `circleout` -> `O_large_outer`
   - `smallcirclein` -> `IS_small_inner`
   - `smallcircleout` -> `IO_small_outer`
   - `cut` -> `S_split_gap`

2. **Convert units explicitly**
   - geometry: convert to µm if preferred
   - resonance columns: choose either wavelength or frequency and convert consistently

3. **Drop incomplete rows**
   - especially rows with missing amplitude targets

4. **Document constraints**
   - enforce `IO >= IS`
   - ensure all geometry values remain within the study bounds

5. **Separate forward and inverse datasets**
   - forward: geometry -> resonance
   - inverse: resonance -> geometry

---

## 11. Example cleaned variable set
A practical cleaned dataset for forward regression may use:

### Inputs
- `I_large_inner`
- `O_large_outer`
- `S_split_gap`
- `IS_small_inner`
- `IO_small_outer`

### Targets
- `lambda_1` or `f1`
- `lambda_2` or `f2`
- `A1_percent`
- `A2_percent`

---

## 12. Citation / acknowledgment note
If you use this dataset in a report, thesis, or follow-up paper, cite the associated paper and describe the dataset as a simulation-derived dataset of geometric parameters and dual-resonance response variables for a merged circular split-ring THz absorber.

---

## 13. Short summary
This dataset is a **simulation-derived regression dataset** for a **dual-band THz metamaterial absorber**. It connects **resonator geometry** to **two resonance locations** and **their absorption amplitudes**, and it was used for both **forward prediction** and **inverse redesign** in the associated paper.
