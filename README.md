# ESG Performance and Financial Performance: Panel Regression Analysis 🌱📊

Python analysis of the relationship between firms' ESG ratings and their Return on Assets (ROA) in a panel of 100 companies over 2019-2024. It builds the dataset, produces descriptive tables and trend figures, runs Pooled OLS, Fixed Effects and Random Effects regressions, and reports diagnostic tests.

> **Suggested repository name:** `esg-roa-panel-regression`

## Research Question 🎯
Is a firm's ESG letter grade associated with its financial performance, measured by ROA, after controlling for firm size, leverage and revenue growth?

**Model estimated:**

```
ROA = b0 + b1·ESG_Score + b2·FirmSize + b3·Leverage + b4·RevGrowth + e
```

## Features ✨
- Flexible loader for `.xlsx`, `.xls`, `.xlsm` or `.csv` (tries several text encodings for CSV files)
- Fuzzy column-name matching so small header differences do not break the script
- ESG letter grade (AAA to D) converted to a 1-10 numeric score
- Derived variables: ROA, Firm Size, Leverage, Revenue Growth
- Automatic handling of infinite values and duplicate firm-years
- Descriptive statistics and correlation matrix
- Pooled OLS (robust SE), Fixed Effects and Random Effects (clustered SE)
- Diagnostics: VIF, Breusch-Pagan, Durbin-Watson, Hausman test
- Trend tables and figures for ESG and ROA (mean, median, dispersion and outliers)
- All tables saved as CSV and all figures saved as PNG

## Prerequisites 🛠️
Before running this project, ensure you have the following installed:
- Python 3.9 or higher
- The following Python packages:

```bash
pip install numpy pandas matplotlib scipy statsmodels linearmodels openpyxl
```

> `openpyxl` is needed to read Excel files.

## Installation 📦
Get your environment running by following these steps:

```bash
# Clone the repository
git clone https://github.com/<your-username>/esg-roa-panel-regression.git

# Navigate into the directory
cd esg-roa-panel-regression

# (Optional) create a virtual environment
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

## Project Structure 📁
```
esg-roa-panel-regression/
├── README.md
├── requirements.txt
├── esg_roa_panel_analysis.py     # main script: data build, tables, regressions, diagnostics
├── roa_trend_summary.py          # short snippet: average ROA trend by year
├── additional_figures.py         # extra figures: mean vs median ROA, ROA boxplot, ESG trend
├── data/
│   └── Excel 1.xlsx              # not tracked, add locally
├── output/                       # generated tables (CSV) and figures (PNG)
└── .gitignore                    # should exclude data/ and output/
```

> `roa_trend_summary.py` and `additional_figures.py` assume the `df` DataFrame already exists, so run them after the main script (same session or notebook), or import the data-building section into them.

## Data 📊
The input is a manually collected firm-year dataset with these columns (header matching is flexible):

| Column | Description |
| --- | --- |
| `Company` | Company name |
| `Industry` | Industry or sector (renamed to `Sector`) |
| `Year` | Fiscal year (2019-2024) |
| `ESG Letter Grade` | ESG rating, AAA to D |
| `Total Assets (US$ bn)` | Total assets |
| `Total Debt (US$ bn)` | Total debt |
| `Revenue (US$ bn)` | Revenue |
| `Net Income (US$ bn)` | Net income |

**Sample:** 600 firm-year observations, 100 companies, 6 years each (balanced panel), all values in US$ billions.

**ESG score mapping:**

| Letter | AAA | AA | A | BBB | BB | B | CCC | CC | C | D |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Score | 10 | 9 | 8 | 7 | 6 | 5 | 4 | 3 | 2 | 1 |

**Derived variables:**

| Variable | Definition |
| --- | --- |
| `ROA` | Net Income / Total Assets |
| `FirmSize` | ln(Total Assets in US$) |
| `Leverage` | Total Debt / Total Assets |
| `RevGrowth` | (Revenue_t - Revenue_t-1) / Revenue_t-1, computed within each company |

> **Data availability:** The raw workbook is not included in this repository.

## Usage 💡
Run the scripts in this order:

```bash
python esg_roa_panel_analysis.py
python roa_trend_summary.py
python additional_figures.py
```

Output is printed to the console and written to the output folder.

| Output file | Contents |
| --- | --- |
| `master_dataset.csv` | Cleaned dataset with derived variables |
| `table_4_1_sample.csv` | Sample characteristics by company |
| `table_4_2_esg_by_year.csv` | ESG score by company and year |
| `table_4_2b_esg_summary_by_year.csv` | ESG mean, median, SD by year |
| `table_4_3_roa_by_year.csv` | ROA by company and year |
| `table_4_3b_roa_summary_by_year.csv` | ROA mean, median, SD, min, max by year |
| `table_4_4_descriptive_stats.csv` | Descriptive statistics |
| `table_4_5_correlation_matrix.csv` | Correlation matrix |
| `table_4_6_panel_regression.csv` | Pooled OLS, FE and RE coefficients |
| `table_4_6b_fit_stats.csv` | R², F-statistics, N |
| `table_4_7_diagnostics.csv` | VIF, Breusch-Pagan, Durbin-Watson, Hausman |
| `fig_4_1_esg_trend.png` | Mean ESG score with ±1 SD band |
| `fig_4_2_roa_trend.png` | Mean ROA with ±1 SD band |
| `fig_4_3_roa_mean_vs_median.png` | Mean vs median ROA |
| `fig_4_4_roa_boxplot_by_year.png` | ROA distribution by year |
| `fig_4_5_esg_trend_mean_median.png` | ESG mean vs median with ±1 SD band |

## Configuration ⚙️
The original script uses hard-coded Windows paths. Update them at the top of the main script:

```python
# Original
INPUT_CSV = r"C:\Users\Downloads\1111\Excel 1.xlsx"
OUT_DIR = r"C:\Users\Downloads\New folder (4)"

# Replace with relative paths
INPUT_CSV = "data/Excel 1.xlsx"
OUT_DIR = "output"
```

The variable is named `INPUT_CSV` but accepts Excel files too. Consider renaming it to `INPUT_FILE`.

## Methodology 🔬
1. **Load and clean:** read the file, match columns, trim text, convert numbers, drop rows missing `Company` or `Year`.
2. **Build variables:** map ESG grades, compute ROA, FirmSize, Leverage and RevGrowth, replace ±inf with NaN, drop duplicate (Company, Year) rows.
3. **Describe:** descriptive statistics, correlations, ESG and ROA trends.
4. **Estimate:** Pooled OLS with HC1 robust SE, entity Fixed Effects and Random Effects with SE clustered by firm. If ESG is absorbed by the fixed effects, the script re-fits with `drop_absorbed=True`.
5. **Diagnose:** VIF, Breusch-Pagan (on pooled OLS residuals), Durbin-Watson, manual Hausman test.

## Results 📈

### Sample
- 600 firm-year observations, 100 companies, 2019-2024
- 8 observations had infinite values (e.g. zero total assets) and were set to missing
- **Regression sample: 407 observations across 87 firms** (first-year `RevGrowth` is undefined, and other missing values were dropped)

### Descriptive statistics (Table 4.4)

| Variable | Mean | Median | Std. Dev. | Min | Max | N |
| --- | --- | --- | --- | --- | --- | --- |
| ESG_Score | 7.197 | 7.000 | 1.189 | 2.000 | 9.000 | 600 |
| ROA | -0.131 | 0.017 | 2.785 | -65.455 | 5.000 | 592 |
| FirmSize | 20.078 | 20.166 | 2.537 | 13.582 | 26.996 | 592 |
| Leverage | 0.185 | 0.090 | 0.362 | 0.000 | 5.655 | 592 |
| RevGrowth | 0.676 | 0.000 | 14.266 | -13.728 | 286.272 | 408 |

### Correlation matrix (Table 4.5)

| | ESG_Score | ROA | FirmSize | Leverage | RevGrowth |
| --- | --- | --- | --- | --- | --- |
| **ESG_Score** | 1.000 | 0.021 | 0.184 | -0.037 | -0.020 |
| **ROA** | 0.021 | 1.000 | 0.143 | -0.122 | -0.110 |
| **FirmSize** | 0.184 | 0.143 | 1.000 | 0.075 | -0.107 |
| **Leverage** | -0.037 | -0.122 | 0.075 | 1.000 | 0.030 |
| **RevGrowth** | -0.020 | -0.110 | -0.107 | 0.030 | 1.000 |

### Trends by year

| Year | Mean ROA | Median ROA | Std Dev ROA | Min ROA | Max ROA | Mean ESG | Median ESG |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 2019 | 0.0269 | 0.0147 | 0.3704 | -1.5387 | 2.5 | 7.15 | 7.0 |
| 2020 | 0.0275 | 0.0153 | 0.3668 | -1.5387 | 2.5 | 7.17 | 7.0 |
| 2021 | 0.0239 | 0.0201 | 0.3609 | -1.5387 | 2.5 | 7.21 | 7.0 |
| 2022 | -0.6617 | 0.0265 | 6.5917 | -65.4547 | 5.0 | 7.19 | 7.0 |
| 2023 | -0.0509 | 0.0188 | 1.1084 | -9.0422 | 5.0 | 7.24 | 7.0 |
| 2024 | -0.1448 | 0.0030 | 0.9577 | -6.0533 | 5.0 | 7.22 | 7.0 |

- **ESG scores are very stable**: the mean stays around 7.2 and the median at 7.0 in every year.
- **Mean ROA is distorted by outliers**: the 2022 mean (-0.66) is driven by an extreme loss-making firm-year (ROA = -65.45). The median stays near 0.02 throughout, which is a better picture of typical performance.

### Panel regression (Table 4.6), dependent variable: ROA

| Variable | Pooled OLS | Fixed Effects | Random Effects |
| --- | --- | --- | --- |
| const | -1.4834 (p = .005) | -15.4618 (p = .095) | -1.6347 (p = .017) |
| **ESG_Score** | **0.0018 (p = .843)** | **-0.0499 (p = .260)** | **-0.0095 (p = .577)** |
| FirmSize | 0.0737 (p = .005) | 0.7701 (p = .092) | 0.0864 (p = .014) |
| Leverage | -0.4181 (p = .006) | -0.5662 (p < .001) | -0.5524 (p = .003) |
| RevGrowth | -0.0031 (p < .001) | 0.0002 (p = .696) | -0.0015 (p = .019) |

Standard errors: HC1 robust (Pooled OLS), clustered by firm (FE, RE).

| Model | R² | F-stat | N |
| --- | --- | --- | --- |
| Pooled OLS | 0.1545 | 50.22 | 407 |
| Fixed Effects | 0.3023 | 34.22 | 407 |
| Random Effects | 0.1593 | 19.04 | 407 |

### Diagnostics (Table 4.7)

| Test | Result | Interpretation |
| --- | --- | --- |
| VIF | All regressors between 1.01 and 1.03 | No multicollinearity concern |
| Breusch-Pagan | LM = 17.02, p = .0019 | Heteroskedasticity present, so robust or clustered SE are appropriate |
| Durbin-Watson | 1.773 | Close to 2, no strong first-order autocorrelation |
| Hausman | χ² = 6.67, df = 5, p = .247 | Fail to reject H0, Random Effects preferred |

### Key takeaways 🔑
- **ESG is not significantly related to ROA in any specification.** The coefficient is near zero and changes sign across models (+0.0018, -0.0499, -0.0095), all with p > .25.
- **Leverage is negatively associated with ROA** in all three models (p < .01), and the effect is strongest in the Fixed Effects model.
- **Firm size** is positively related to ROA in the Pooled and Random Effects models, but only marginally in Fixed Effects.
- **Revenue growth** is significantly negative in Pooled OLS and Random Effects but not in Fixed Effects.
- The Hausman test favours Random Effects, but see the caveats below.

## Known Limitations ⚠️
- **Extreme outliers:** ROA ranges from -65.45 to 5.00 and RevGrowth reaches 286. Results may be driven by a few observations. Consider winsorizing (e.g. at 1% and 99%) or excluding extreme values and re-running the regressions.
- **Suspicious ROA bounds:** The minimum and maximum ROA are identical across several years (-1.5387 and 2.5 in 2019-2021, and 5.0 in 2022-2024). Check the source data for entry errors, unit mismatches or capped values.
- **Slow-moving ESG grades:** Because ratings barely change within firms, fixed effects absorb most of the ESG variation, so the ESG coefficient in the FE model is identified from few firms and is imprecise.
- **Hausman test degrees of freedom:** The test includes the constant (df = 5). With four slope coefficients the degrees of freedom should be 4. Also, with clustered standard errors the classical Hausman test is not strictly valid, so consider a robust (e.g. Mundlak or bootstrap) version.
- **Adjusted R²:** For the FE and RE models, the `Adj_R2` column in `table_4_6b_fit_stats.csv` simply repeats R² and is not a true adjusted value. FE R² is the within-R².
- **Sample label:** Figure titles say "FTSE 100 Sample", but the listed companies are mostly small oil and gas, renewable energy and hydrogen firms. Confirm the sample description before reporting.
- **Missing observations:** 8 ROA values and many RevGrowth values are missing, reducing the regression sample from 600 to 407 observations. Zero total assets also trigger a `divide by zero in log` warning.
- **No causal claim:** The models show association only. Reverse causality and omitted variables (industry, year effects) are not addressed. Consider adding year dummies and sector controls.

## Contributing 🤝
Contributions are welcome! Please open an issue or submit a pull request for any changes.

## License 📄
This project is licensed under the MIT License - see the `LICENSE` file for details. The data are not covered by this license and are not distributed here.
