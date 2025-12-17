# Raman Spectroscopy Analysis for Choline Phosphate

Temperature-dependent conformational analysis of choline phosphate via Raman spectroscopy with two-round fitting, residual-based outlier detection, and interactive peak selection.

## Overview

This software provides automated analysis of temperature-dependent Raman spectroscopy data for choline phosphate systems. The analysis focuses on conformational changes in the 680-810 cm⁻¹ spectral region, distinguishing between gauche and anti conformers through Voigt profile fitting.

**Key Features:**
- Interactive peak selection via graphical user interface
- Two-round fitting approach: initial fit identifies outliers, second fit excludes them
- Residual-based outlier detection using robust statistics (MAD method)
- Automatic baseline correction using asymmetrically reweighted penalized least squares (arPLS)
- Publication-ready visualization with high-resolution output
- Temperature-resolved conformer ratio calculation

## Citation

If you use this software in your research, please cite:

```
Hessling, J. (2025). Raman Spectroscopy Analysis for Choline Phosphate (Version 1.0) 
[Computer software]. https://github.com/janishessling/choline-conformer-raman-analysis
```

## Installation

### System Requirements

- Python 3.8 or higher
- 4 GB RAM minimum (8 GB recommended for large datasets)
- Display capable of showing GUI windows (minimum 1024x768 resolution)

### Dependencies

Install all required packages using pip:

```bash
pip install -r requirements.txt
```

Required packages:
- numpy (≥1.20.0): Numerical computing
- pandas (≥1.3.0): Data manipulation and CSV output
- matplotlib (≥3.4.0): Plotting and visualization
- scipy (≥1.7.0): Scientific computing and integration
- lmfit (≥1.0.0): Non-linear least squares fitting
- rampy (≥0.4.0): Baseline correction algorithms

## Input Data Format

### File Organization

Place all Raman spectroscopy data files in a single folder. The program will process all `.txt` files in the selected directory.

### File Naming Convention

Filenames must contain temperature information in one of these formats:

**Accepted formats:**
- `25C_sample.txt` - Temperature followed by C
- `25°C_sample.txt` - Temperature with degree symbol
- `25degC_sample.txt` - Temperature with "deg"
- `140C_measurement_001.txt` - Temperature anywhere in filename
- `RT_sample.txt` - Room temperature (interpreted as 25°C)

**Examples of valid filenames:**
```
25C_Chol_H2PO4_spectrum.txt
140degC_measurement_replicate1.txt
RT_baseline_corrected.txt
sample_100C_final.txt
```

**Invalid filenames (will be skipped):**
```
sample.txt (no temperature)
measurement_data.txt (no temperature)
25K_sample.txt (wrong unit, use Celsius)
```

### File Content Format

Data files must be tab- or space-separated text files with two columns:

**Column 1:** Wavenumber in cm⁻¹  
**Column 2:** Intensity (arbitrary units)

**Example file content:**
```
680.0    1234.5
681.0    1245.2
682.0    1256.8
683.0    1268.4
...
809.0    2345.1
810.0    2356.7
```

**Format specifications:**
- Decimal separator: period (`.`) not comma (`,`)
- Column separator: single or multiple spaces, or tab
- No header row (data starts immediately)
- Comments: lines starting with `#` are automatically ignored
- Wavenumber range: should cover at least 680-810 cm⁻¹
- Data points: minimum 100 points recommended for reliable fitting

**Common issues and solutions:**

| Issue | Solution |
|-------|----------|
| Comma as decimal separator | Replace `,` with `.` in your text editor |
| Excel CSV format | Save as space-delimited `.txt` instead |
| Multiple columns | Use only first two columns (wavenumber and intensity) |
| Header present | Remove header row or add `#` at the beginning |

### Data Quality Requirements

For optimal results, ensure your input data meets these criteria:

**Spectral range:**
- Must include the region 680-810 cm⁻¹
- Can extend beyond this range (will be automatically cropped)
- Recommended: 600-900 cm⁻¹ for better baseline correction

**Signal quality:**
- Signal-to-noise ratio: >10:1 recommended
- Baseline should be relatively flat (arPLS will correct remaining curvature)
- Cosmic rays and detector artifacts are automatically detected and excluded

**Sampling:**
- Wavenumber step size: 0.5-2.0 cm⁻¹ recommended
- Minimum 100 data points in the 680-810 cm⁻¹ region
- Even spacing preferred but not required

## Usage

### Basic Workflow

Run the analysis script:

```bash
python Choline_Raman_Analysis_GitHub.py
```

The program will guide you through the following steps:

**Step 1: Folder Selection**
- A file dialog opens
- Navigate to your data folder
- Select the folder containing your `.txt` files
- Click "Select Folder"

**Step 2: Temperature Range Configuration**
- Choose between automatic or manual mode
- **Automatic mode**: Uses all available data, auto-scales plots
- **Manual mode**: Set custom ranges for data filtering and color scales

Manual mode options:
- Data range: Exclude spectra outside this temperature range
- Color scale: Set min/max temperatures for plot color mapping

Example use case for manual mode:
- Analyze 15-140°C but use -40 to 140°C color scale for comparison with literature data

**Step 3: Interactive Peak Picking**
- For each spectrum, a window displays the baseline-corrected data
- Click on peak maxima you want to fit
- Peaks are automatically classified:
  - Gauche conformer: <740 cm⁻¹
  - Anti conformer: 740-790 cm⁻¹
  - Dihydrogen phosphate: ≥790 cm⁻¹

Controls:
- Left click: Add peak
- Right click: Remove nearest peak
- "Done" button: Confirm and proceed to next spectrum
- "Reset" button: Clear all selected peaks
- "Skip Spectrum" button: Exclude this spectrum from analysis

**Step 4: Voigt Fraction Optimization**
- The program tests different Gaussian/Lorentzian mixing ratios
- Progress is displayed in the console
- Optimal fraction is automatically selected based on chi-square values

**Step 5: Fitting All Spectra**
- Two-round fitting is performed for each spectrum
- Round 1: Fit all data points
- Outlier detection: Flag points with residuals >3σ from fit
- Round 2: Refit excluding outliers
- Progress is displayed with chi-square values and outlier counts

**Step 6: Results**
- Output files are saved in your data folder
- Analysis summary is printed to console

### Advanced Configuration

Edit the constants at the beginning of the script to customize behavior:

**Outlier detection sensitivity:**
```python
OUTLIER_THRESHOLD_SIGMA = 3.0  # Standard (99.7% confidence)
# Set to 2.5 for more aggressive outlier removal
# Set to 4.0 for more conservative approach
```

**Peak region:**
```python
PEAK_REGION_MIN = 680  # cm⁻¹
PEAK_REGION_MAX = 810  # cm⁻¹
```

**Peak classification thresholds:**
```python
GAUCHE_THRESHOLD = 740.0      # cm⁻¹, gauche/anti boundary
PHOSPHATE_THRESHOLD = 790.0   # cm⁻¹, anti/phosphate boundary
```

**Plot styling:**
```python
OUTPUT_DPI = 300              # Resolution for saved plots
PLOT_LINEWIDTH_NORMAL = 2.0   # Line width for spectra
PLOT_ALPHA_MEDIUM = 0.7       # Transparency for overlays
```

## Output Files

All output files are saved in your input data folder.

### Main Results

**analysis_results.csv**
Comma-separated file containing:
- temperature: Measurement temperature in °C
- filename: Original data filename
- chi_square: Goodness of fit (lower is better)
- n_outliers_excluded: Number of points excluded in Round 2
- conformer_ratio: Gauche/(Gauche+Anti) ratio
- gauche_area, anti_area, phosphate_area: Integrated peak areas
- peak1_center, peak1_width, peak1_area: Parameters for each fitted peak
- Additional columns for each peak (center, width, area, type)

### Visualization Outputs

**Output_1A_Temperature_Spectra_Normalized.png**
- Overlay of all spectra normalized by total conformer peak area
- Color-coded by temperature (blue = cold, red = hot)
- Shows temperature-dependent spectral changes
- Outliers removed for clean visualization

**Output_1B_Temperature_Spectra_Raw.png**
- Overlay of all spectra with original intensities
- Same color coding as 1A
- Useful for comparing absolute signal intensities

**Output_2A_Best_Fit.png**
- Example of the spectrum with lowest chi-square value
- Shows measured data, total fit, and individual peak components
- Residual plot below main spectrum
- Outliers marked with red X symbols

**Output_2B_Worst_Fit.png**
- Example of the spectrum with highest chi-square value
- Useful for identifying problematic data
- Same layout as 2A

**Output_3_Conformer_Ratio.png**
- Conformer ratio vs. temperature plot
- Error bars show uncertainty from peak fitting
- Useful for identifying conformational transitions

**Outlier_Detection/ folder**
Contains individual plots for each spectrum showing:
- Top panel: Measured data, Round 1 fit, detected outliers
- Bottom panel: Residuals with ±3σ threshold lines
- Filename format: `Outliers_[temperature]C.png`

## Methodology

### Two-Round Fitting Approach

Traditional outlier removal by interpolation can introduce artifacts. This software uses a two-round approach:

**Round 1:**
- Fit all data points using user-selected peak positions
- Calculate residuals: measured - fitted
- Compute robust statistics using Median Absolute Deviation (MAD)
- Flag points where |residual - median| > 3σ_robust

**Round 2:**
- Refit spectrum excluding flagged outliers
- Use identical initial peak positions as Round 1
- Result: improved fit quality without interpolation artifacts

**Advantages:**
- No artificial data introduced by interpolation
- Narrow spectral features preserved
- Cosmic rays and detector artifacts reliably identified
- Transparent: outliers are documented and visualized

### Conformer Classification

Peaks are classified based on literature assignments for choline phosphate:

| Wavenumber Range | Assignment | Variable Name |
|-----------------|------------|---------------|
| <740 cm⁻¹ | Gauche conformer | gauche_area |
| 740-790 cm⁻¹ | Anti conformer | anti_area |
| ≥790 cm⁻¹ | H₂PO₄⁻ vibrations | phosphate_area |

**Conformer ratio calculation:**
```
Conformer Ratio = Gauche Area / (Gauche Area + Anti Area)
```

Note: Phosphate peaks are excluded from conformer ratio as they do not reflect C-N bond conformation.

### Peak Fitting Model

Each peak is fitted with a pseudo-Voigt profile:

```
V(x) = η·L(x) + (1-η)·G(x)
```

Where:
- L(x): Lorentzian component (accounts for lifetime broadening)
- G(x): Gaussian component (accounts for Doppler and instrumental broadening)
- η: Mixing parameter (0 = pure Gaussian, 1 = pure Lorentzian)

The optimal η is determined automatically by testing values from 0.0 to 1.0 in 0.1 increments and selecting the value that minimizes the average chi-square across all spectra.

### Baseline Correction

Baseline correction uses the arPLS (asymmetrically reweighted penalized least squares) algorithm:

**Parameters:**
- λ = 10⁶: Smoothness parameter (larger = smoother baseline)
- ratio = 0.01: Asymmetry parameter for peak preservation
- Iterative reweighting: Automatically adapts to spectral features

After arPLS, a linear shift is applied to ensure the minimum intensity is at zero, preventing negative values that would complicate peak area integration.

## Troubleshooting

### File Loading Issues

**Problem:** "No .txt files found in selected folder"  
**Solution:** 
- Verify files have `.txt` extension (not `.csv` or `.dat`)
- Check that files are directly in the folder, not in subfolders
- Ensure files are not hidden by your operating system

**Problem:** "No temperature found in filename"  
**Solution:**
- Add temperature to filename: `sample.txt` → `25C_sample.txt`
- Use supported formats: `25C`, `25°C`, `25degC`, or `RT`
- Temperature must be a number followed immediately by C or degC

**Problem:** "Error loading file: [filename]"  
**Solution:**
- Check file format: two columns, space or tab separated
- Remove header rows or prefix them with `#`
- Replace comma decimal separators with periods
- Verify file is not corrupted or locked by another program

### Peak Picking Issues

**Problem:** Cannot click on peaks in interactive window  
**Solution:**
- Ensure window is in focus (click on title bar)
- Try closing and reopening the window
- Check that your display resolution is at least 1024x768
- On some systems, press Tab to activate the plot area

**Problem:** Peak picker window appears behind other windows  
**Solution:**
- Look in taskbar for the Python/Matplotlib window
- On Windows: Alt+Tab to cycle through windows
- On Mac: Command+Tab to cycle through applications

**Problem:** Selected peak positions are not accurate  
**Solution:**
- Click directly on the peak maximum
- If spectrum is noisy, smooth data before analysis
- Zoom in using the matplotlib toolbar for better precision

### Fitting Issues

**Problem:** High chi-square values (>0.01)  
**Solution:**
- Check that baseline correction worked properly
- Verify peak positions are reasonable (not in baseline regions)
- Increase outlier threshold if legitimate data is being excluded
- Check for severe spectral artifacts or contamination

**Problem:** Negative peak areas reported  
**Solution:**
- This should not occur with proper baseline correction
- Check that PEAK_REGION_MIN and PEAK_REGION_MAX are appropriate
- Verify baseline is not tilted (adjust arPLS parameters if needed)

**Problem:** Unrealistic conformer ratios (outside 0-1 range)  
**Solution:**
- Verify gauche and anti peaks are correctly classified
- Check GAUCHE_THRESHOLD value (should be ~740 cm⁻¹)
- Ensure peak areas are positive

### Memory Issues

**Problem:** "MemoryError" or program crashes with many files  
**Solution:**
- Process files in smaller batches
- Close other applications to free RAM
- Reduce OUTPUT_DPI if memory constrained
- Consider using a 64-bit Python installation

### Display Issues

**Problem:** Plots do not render properly or appear blank  
**Solution:**
- Update matplotlib: `pip install --upgrade matplotlib`
- Try different backend: add `matplotlib.use('TkAgg')` before imports
- Check display drivers are up to date
- On remote systems: ensure X11 forwarding is enabled

**Problem:** GUI windows freeze or become unresponsive  
**Solution:**
- Do not click rapidly on the plot area
- Wait for each operation to complete
- Close and restart if window becomes frozen
- Check system resources (CPU, RAM)

## Performance Considerations

### Processing Time

Typical processing times on a modern desktop computer (i5/i7, 8GB RAM):

| Number of Spectra | Peak Picking | Fitting | Total Time |
|------------------|--------------|---------|------------|
| 5-10 spectra | 2-5 min | 1-2 min | 3-7 min |
| 20-30 spectra | 5-10 min | 3-5 min | 8-15 min |
| 50+ spectra | 10-20 min | 5-10 min | 15-30 min |

Peak picking time depends on user speed. Fitting time scales approximately linearly with number of spectra and peaks.

### Optimization Tips

**For faster processing:**
- Select fewer peaks (only the essential conformer peaks)
- Use automatic mode instead of manual temperature ranges
- Close unnecessary applications to free system resources
- Process large datasets overnight

**For better results:**
- Take time during peak picking to select accurate positions
- Review outlier detection plots to verify proper outlier identification
- Check best/worst fit examples to ensure fit quality
- Rerun with adjusted parameters if initial results are unsatisfactory

## Known Limitations

1. **Overlapping peaks:** Severely overlapping peaks may not be fully resolved. Consider using higher-resolution spectroscopy if available.

2. **Temperature extraction:** Room temperature (RT) is assumed to be 25°C. Adjust manually in code if different.

3. **Single conformer systems:** If only one conformer is present, the conformer ratio becomes meaningless (0 or 1). Manual interpretation required.

4. **Non-Voigt line shapes:** Some systems may exhibit asymmetric or complex line shapes not well described by Voigt profiles.

5. **Batch processing:** Currently requires manual peak picking for each spectrum. For identical samples, consider using same peak positions across all spectra (requires code modification).

## Contributing

Contributions are welcome. Please follow these guidelines:

1. Fork the repository
2. Create a feature branch: `git checkout -b feature-name`
3. Make your changes with clear commit messages
4. Add tests if applicable
5. Update documentation as needed
6. Submit a pull request with description of changes

Please report bugs or feature requests via GitHub Issues.

## License

MIT License

Copyright (c) 2025 Janis Hessling

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

## Contact

Janis Hessling  
Email: hesslingjanis@gmail.com 
GitHub: https://github.com/janishessling
ORCID: https://orcid.org/0009-0009-1312-1278  


Project Link: https://github.com/janishessling/choline-conformer-raman-analysis
## Acknowledgments

This software uses the following open-source packages:
- NumPy and SciPy: Fundamental scientific computing
- Matplotlib: Plotting and visualization
- Pandas: Data manipulation and analysis
- lmfit: Non-linear least squares minimization
- rampy: Baseline correction algorithms (arPLS implementation)

Special thanks to the scientific Python community for providing these essential tools.

## Version History

**Version 1.0 (2025-12)**
- Refactored GUI functions for better maintainability
- Added 14 plot styling constants for consistency
- Improved input validation and error messages
- Enhanced documentation with detailed troubleshooting
- Better code structure following Python best practices
- Added comprehensive output file descriptions

**Version 0.5 (2025-12)**
- Initial release
- Two-round fitting with residual-based outlier detection
- Interactive peak picking interface
- Temperature-resolved conformer analysis
- Automatic baseline correction
- Publication-ready visualization
