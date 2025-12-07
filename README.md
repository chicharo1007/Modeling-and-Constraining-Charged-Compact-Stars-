# 🌌 TOV Solver with Malliavin Calculus

## Uncertainty Quantification for Neutron Star Models

This project provides a Python-based solver for the **Tolman-Oppenheimer-Volkoff (TOV)** equations, enhanced with **Malliavin Calculus** for stochastic uncertainty quantification.

Designed for Google Colab, this script allows physicists and researchers to upload Equation of State (EoS) files, generate Mass-Radius relations, and mathematically verify the stability of the numerical solutions against stochastic noise.

### 🚀 Key Features

*   **Stochastic TOV Solver:** Solves hydrostatic equilibrium equations using the Euler-Maruyama method with injected noise.
*   **Malliavin Calculus:** Computes the sensitivity of the Mass/Radius solution to the underlying Brownian motion to establish a theoretical variance bound.
*   **Smart Auto-Detection:** Automatically detects Pressure and Density columns (including Log10 density) in uploaded data files.
*   **Consistency Check:** Verifies if the empirical variance of the Monte Carlo simulations respects the Malliavin theoretical bound ($Var[F] \le E[\|DF\|^2]$).
*   **Visualization:** Plots Mass-Radius curves with uncertainty clouds and consistency ratio bar charts.

---

### 📦 Dependencies

This script is optimized for **Google Colab**.
*   `numpy`: Numerical computations and array handling.
*   `matplotlib`: Plotting Mass-Radius relations and statistical charts.
*   `pandas`: Robust file parsing and data ingestion.
*   `google.colab`: For file upload widgets.

---

### 📖 How to Run

1.  **Open in Google Colab:** Copy the script into a notebook cell.
2.  **Run the Cell:** Execute the code.
3.  **Upload Files:**
    *   A prompt will appear: `Upload your EoS files now...`
    *   Click "Choose Files" and select your `.dat`, `.txt`, or `.thermo` EoS files.
4.  **Automatic Analysis:**
    *   The script will parse the files.
    *   It will run 200 stochastic simulations per file.
    *   It will generate plots and a statistical summary table.

> **Note:** If you do not upload any files, the script will automatically generate synthetic sample data to demonstrate functionality.

---

### 📄 Input Data Format

The script uses a heuristic `autodetect_eos_columns` function to read standard EoS files. Ideally, your files should look like this:

*   **Format:** ASCII text (columns separated by spaces or tabs).
*   **Columns:** Must contain **Pressure (P)** and **Density ($\rho$)**.
*   **Units:** The solver assumes consistent nuclear units (e.g., $fm^{-4}$ or $MeV/fm^3$).
*   **Logarithmic Data:** The script detects if density is provided as $\log_{10}(\rho)$ (usually by checking for negative values) and converts it automatically.

---

### 📊 Interpreting the Results

The script outputs a summary table with a "Consistency Ratio". Here is how to read it:

#### The Malliavin Bound
Malliavin calculus provides a derivative operator $D$. The fundamental inequality checked is:
$$ \text{Empirical Variance} \approx E[(F - E[F])^2] \le E[\|D F\|_{H}^2] $$

#### Output Columns
*   **Max Mass:** The maximum mass supported by the EoS (in $M_{\odot}$).
*   **Empirical Variance:** The actual variance observed across the 200 Monte Carlo paths.
*   **Malliavin Bound:** The theoretical upper bound calculated via the sensitivity Jacobian.
*   **Ratio:** $\frac{\text{Empirical Variance}}{\text{Malliavin Bound}}$

#### Status Codes
*   🟢 **PASS (Ratio $\le$ 1.05):** The model is mathematically consistent. The numerical noise is well-behaved.
*   🔴 **FAIL (Ratio > 1.05):** The empirical variance exceeds the theoretical bound. This suggests numerical instability, overly stiff EoS regions, or integration steps ($dr$) that are too coarse.

---

### ⚠️ Troubleshooting

*   **"Failed to confidently detect columns":** Your file might have too many header lines or non-standard formatting. Ensure standard space-delimited columns.
*   **Step Size:** The default radial step is `dr = 0.01`. If your simulation fails, try reducing this value in the code.
*   **Noise Magnitude:** The default noise is `sigma = 1e-3`. If the variance is too high, reduce sigma.

---

### 📜 License
Open source. Free to use for academic and research purposes.


---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# 🌟 Neutron Star F-Mode Solver & Monte Carlo Analysis

## Physics-Informed Machine Learning & Asteroseismology

This project is a high-precision numerical toolkit designed to solve the **Tolman-Oppenheimer-Volkoff (TOV)** equations and the **Linearized Radial Pulsation** equations.

It takes tabulated Equation of State (EoS) data—specifically from the **CompOSE** database format—and calculates the fundamental oscillation frequency (f-mode), Mass, and Radius of neutron stars. It then performs a Monte Carlo simulation to explore the stability landscape of various nuclear models.

### 🚀 Key Features

*   **Geometrized Unit System:** Internal calculations are performed using $G=c=1$ for numerical stability, with automatic conversion from Nuclear units (MeV/fm³) and SI units.
*   **Eigenfrequency Shooting:** Solves the boundary value problem for radial pulsations to find the fundamental eigenfrequency $\omega_0$.
*   **Monte Carlo Sampling:** Instead of calculating a single star, the script samples a distribution of central pressures ($P_c$) to generate full Mass-Radius-Frequency curves with statistical uncertainty.
*   **CompOSE Compatibility:** Native support for `.thermo` files from the CompOSE nuclear physics database.
*   **Advanced Visualization:** Generates 4 distinct plots including M-R relations colored by frequency, violin plots of stability, and correlation heatmaps.

---

### 📦 Dependencies

This script is optimized for **Google Colab** and uses the scientific Python stack:
*   `numpy` & `scipy`: For ODE integration (`solve_ivp`), root finding (`root_scalar`), and interpolation.
*   `pandas`: Data management for Monte Carlo results.
*   `seaborn` & `matplotlib`: Statistical data visualization.
*   `google.colab`: For file upload widgets.

---

### ⚙️ Input Data Format (CompOSE)

The code is pre-configured to read **CompOSE** `.thermo` files.
*   **File Extension:** `.thermo`
*   **Header:** Skips the first 2 lines.
*   **Units:** Assumes input data is in **MeV/fm³**.
*   **Column Indices:**
    *   **Pressure ($P$):** Column Index 12
    *   **Energy Density ($\epsilon$):** Column Index 13

> **⚠️ Important Configuration:**
> If your data files use different column indices, locate this section in the `__main__` block and adjust the integers:
> ```python
> # 0-indexed column numbers
> P_SI_index = 12
> eps_SI_index = 13
> ```

---

### 📖 How to Run

1.  **Start the Environment:** Open the script in a Google Colab notebook.
2.  **Execute:** Run the cell.
3.  **Upload:** A file upload widget will appear. Select one or more `.thermo` files from your local machine.
4.  **Wait for Computation:**
    *   The code converts units to Geometrized form.
    *   It runs 50 Monte Carlo iterations per EoS file.
    *   It solves the ODEs for hydrostatic equilibrium and pulsation.
5.  **View Results:** The script will automatically output four analytical plots.

---

### 📊 Outputs & Visualization

The tool generates the following visualizations to help interpret the nuclear physics:

1.  **M-R Relation (Bubble Plot):**
    *   X-Axis: Mass ($M_{\odot}$)
    *   Y-Axis: Radius (Geometric Units)
    *   *Color:* Different EoS models.
    *   *Size:* Magnitude of the f-mode frequency ($\omega$).

2.  **Violin Plot:**
    *   Displays the distribution of fundamental frequencies ($\omega$) allowed by each Equation of State.

3.  **2D Density Histogram:**
    *   A heatmap showing the most probable regions in the Mass vs. Frequency phase space.

4.  **Correlation Matrix:**
    *   Quantifies the linear relationships between Central Pressure, Mass, Radius, and Frequency.

---

### 📐 Physics Background

**1. TOV Equations (Structure):**
Determines the static structure of the star:
$$ \frac{dP}{dr} = - \frac{(\epsilon + P)(m + 4\pi r^3 P)}{r(r-2m)} $$
$$ \frac{dm}{dr} = 4\pi r^2 \epsilon $$

**2. Radial Pulsation (Stability):**
Determines the oscillation eigenfrequencies by solving for the displacement $\xi$ and pressure perturbation $\Delta P$:
$$ \frac{d\xi}{dr} = -\left(\frac{3}{r} + \frac{P'}{\Gamma_1 P}\right)\xi - \frac{\Delta P}{\Gamma_1 P} $$
...coupled with the equation for $\Delta P$. The solver "shoots" for $\omega^2$ such that the boundary conditions at the surface are satisfied.

---

### 📜 License
Open Source. Free for academic and educational use in relativistic astrophysics.






