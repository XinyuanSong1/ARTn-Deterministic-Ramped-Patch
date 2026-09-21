# ARTn-Deterministic-Ramped-Patch

Modified source files incorporating a deterministic searching method and a ramped escape threshold into the Activation-Relaxation Technique nouveau (ARTn) software.

---

## 1. Prerequisites and Supported Versions

### 1a. Core Software
You must possess the base installation of the ARTn code.
**Repository:** [https://gitlab.com/groupe_mousseau/artn](https://gitlab.com/groupe_mousseau/artn)

> **[IMPORTANT NOTE]:** Access to the core ARTn repository may require permission from Dr. Normand Mousseau.

### 1b. Tested Compatibility
This patch was successfully tested against the latest version of core ARTn available as of **September 21, 2026**.

---

## 2. Installation Instructions

To integrate the new functionalities, you must replace several existing ARTn files with the ones provided in this distribution. **It is highly recommended to back up your original installation before proceeding.**

Follow these steps:

1. Locate the directory where you have installed core ARTn (referred to here as `$ARTN_ROOT`).
2. Copy the contents of the `./source` folder from this distribution into the `$ARTN_ROOT` directory.
   * *You must choose to overwrite existing files when prompted.*
3. Navigate to the `$ARTN_ROOT/source` compile directory and recompile ARTn following the standard instructions provided in the base software documentation (e.g., `make pc_shared_lammps_mpi`).

---

## 3. Usage and Configuration Guide

The new methods are accessed by configuring environment variables within your `bart.sh` simulation execution file.

### 3a. Deterministic Searching Method Parameters
This method replaces stochastic basin exploration with a deterministic, multi-layered search grid.

*   `Type_of_Events`: Defines the activation method. Set to `deterministic` to use this new method.
*   `NDIRS`: Controls the density of the search directions on a unit sphere. *Default: 500.* (Higher values increase search density, but increase computational cost.)
*   `EXPLORE_RADIUS_INCREASE`: The increased radius (displacement interval, in Angstroms) for each searching layer.
*   `MAX_RADIUS_LAYER`: The maximum number of searching layers in the gradient search.
*   `Initial_Step_Size`: Size of initial displacement, in Angstroms.

### 3b. Ramped Escape Threshold Parameters
This function automatically ramps the eigenvalue threshold, facilitating escapes from deep energy basins.

*   `Eigenvalue_Threshold`: Initial Eigenvalue threshold for leaving basin.
*   `EIGEN_THRESH_INTERVAL`: Gradient interval step for adjustment.
*   `EIGEN_THRESH_MIN`: Minimum (tightest) limit for ramping.

### 3c. Example `bart.sh` Configuration Block
Copy the following block into your `bart.sh` file to activate both methods with recommended parameters.

```bash
# ==============================================================================
# [NEW FUNCTIONALITY] Configuration Block
# ==============================================================================

# 1. Activate Deterministic Searching Method
setenv Type_of_Events           deterministic
setenv NDIRS                    600     # Density of search grid (default: 500)
setenv EXPLORE_RADIUS_INCREASE  0.1     # Radial step per searching layer (A)
setenv MAX_RADIUS_LAYER         1       # Max number of radial gradient layers
setenv Initial_Step_Size        0.1     # Magnitude of initial displacement (A)

# 2. Activate Ramped Escape Threshold
# Ramping automatically tightens (makes more negative) the threshold.
setenv Eigenvalue_Threshold     -0.5    # Initial Eigenvalue threshold
setenv EIGEN_THRESH_INTERVAL    -0.1    # Step gradient for adjustment
setenv EIGEN_THRESH_MIN         -1.0    # Absolute limit for ramping

# ==============================================================================
