# World Happiness Analysis: What Drives Happiness Across Countries?

An exploratory and regression analysis of national happiness scores for **148 countries (2015–2023)**, examining how GDP, social factors and **inflation** relate to well-being.

**Tools:** Python · pandas · NumPy · Matplotlib · Seaborn · scikit-learn

---

## 1. Business Question

Which factors are most strongly associated with happiness across countries, and does **inflation** explain anything beyond the usual socioeconomic drivers?

This was broken down into six research questions:

1. How is happiness distributed across countries and regions?
2. Which socioeconomic factors are most strongly associated with happiness?
3. Is higher GDP per capita associated with higher happiness?
4. Is inflation negatively associated with happiness?
5. Does the inflation–happiness relationship differ across income levels?
6. Does inflation add explanatory power once socioeconomic factors are controlled for?

## 2. Dataset

| Item | Detail |
|---|---|
| File | `data/WHI_Inflation.csv` |
| Source | |
| Size | 1,232 raw rows → **1,230** after cleaning, 16 columns |
| Coverage | 148 countries, 10 regions, 2015–2023 |
| Target | `Score` (happiness score, observed range 1.86–7.84) |
| Socioeconomic factors | GDP per Capita, Social support, Healthy life expectancy, Freedom to make life choices, Generosity, Perceptions of corruption (scaled index values, not raw USD) |
| Inflation indicators | Headline CPI, Energy CPI, Food CPI, Core CPI, Producer Price, GDP deflator |

## 3. Methodology

1. **Data cleaning**
   - Found duplicate country-year records (Cyprus, 2018 and 2019) with conflicting values; kept the first occurrence so each country-year is one observation.
   - Validated ranges (score and year).
   - Checked missing values: Core CPI (40.4%) and Producer Price Inflation (37.6%) are too sparse to model, so **Headline CPI** (2.6% missing) was used as the main inflation measure.
   - Extreme inflation values (e.g. Zimbabwe 2020: 557%, South Sudan 2016: 380%, Lebanon 2023: 221%) were kept because they reflect real economic crises, and their influence was tested with a ≤ 50% sensitivity check.
2. **Exploratory analysis:** distributions, trends over time, regional and country rankings, income quartiles (GDP).
3. **Relationship analysis:** correlation matrix, GDP vs happiness, inflation vs happiness (overall and within each income group).
4. **Phased analysis:** Pre-COVID (2015–2019), COVID (2020–2021) and Post-COVID (2022–2023).
5. **Regression (OLS, scikit-learn):** three nested models to measure what each block of variables adds:
   - Model 1: GDP only
   - Model 2: GDP + five other socioeconomic factors
   - Model 3: Model 2 + headline inflation
   - Standardized coefficients to compare effect sizes.

## 4. Key Findings

### Happiness differs widely by region and country
- Average score is **5.50** (median 5.54, std 1.14).
- **North America & ANZ (7.18)** and **Western Europe (6.86)** are the happiest regions; **Sub-Saharan Africa (4.26)** and **South Asia (4.47)** are the lowest, a gap of almost 3 points.
- Top countries (average 2015–2023): Finland (7.66), Denmark (7.58), Iceland (7.52). Bottom: Afghanistan (2.99), Central African Republic (3.20), South Sudan (3.27).

![Happiness by region](figures/happiness_by_region.png)

### GDP is the strongest single driver, but not the only one
- Correlation with happiness: **GDP 0.74**, life expectancy 0.70, social support 0.66, freedom 0.57, corruption perception 0.44, generosity 0.10.
- Average happiness rises steadily across GDP quartiles, from **4.36 (lowest) to 6.50 (highest), a 2.14-point gap**.
- GDP alone explains **54.7%** of the variance in happiness (R² = 0.547).

![GDP vs happiness](figures/gdp_vs_happiness.png)

### Inflation has a negative but weak link to happiness
- Headline inflation correlation with happiness: **−0.20** (Core CPI: −0.23; Producer prices: −0.08). Excluding extreme values (> 50%) gives −0.24 (n = 1,179), so the result is not driven by outliers.
- The relationship is negative in **every** income group, and strongest in the **highest-income quartile (−0.48)**, compared with −0.15 (low), −0.26 (medium-low) and −0.14 (medium-high).
- Inflation is highest in low-income countries (average 11.9% vs 4.4–7.0% elsewhere), yet happiness there is more strongly tied to other factors.

![Inflation vs happiness](figures/inflation_vs_happiness.png)

### Inflation adds little once other factors are included

| Model | R² |
|---|---|
| 1. GDP only | 0.547 |
| 2. Socioeconomic factors (6 variables) | 0.765 |
| 3. Socioeconomic factors + inflation | 0.771 |

- Adding inflation increases R² by only about **0.5 percentage points** (0.766 → 0.771 on the same 1,197 observations).
- Standardized coefficients (effect size): GDP **0.41**, life expectancy **0.35**, social support 0.22, freedom 0.20, corruption perception 0.11, generosity 0.10, **inflation −0.08**.

### Happiness did not fall during COVID-19
| Period | Average happiness |
|---|---|
| Pre-COVID (2015–2019) | 5.44 |
| COVID (2020–2021) | 5.56 |
| Post-COVID (2022–2023) | 5.60 |

- Biggest improvers (first to last year): Romania (+1.47), Guinea (+1.42), Togo (+1.30), Bulgaria (+1.25), Hungary (+1.24).
- Biggest declines: **Lebanon (−2.45)**, Afghanistan (−1.72), Zimbabwe (−1.41), Lesotho (−1.39), Sierra Leone (−1.37). Most are countries affected by economic crisis or conflict.

## 5. Conclusions and Recommendations

- **Income and health matter most.** Policies that raise living standards and life expectancy are associated with the largest gains in national happiness.
- **Inflation is a secondary factor.** It correlates negatively with happiness, but adds very little predictive power beyond the core drivers. Its effect is most visible in higher-income countries, where other basic needs are already met.
- **Crisis countries are the clearest signal.** The largest declines (Lebanon, Afghanistan, Zimbabwe) coincide with severe economic or political instability.

## 6. Limitations and Next Steps

- Results show **association, not causation**; panel data (repeated observations of the same countries) violates the independence assumption of OLS.
- Models are evaluated in-sample (no train/test split) and do not report p-values or confidence intervals.
- Inflation is heavily right-skewed; a log or winsorized transformation could improve the model.
- Next steps: add country fixed effects (panel regression with `statsmodels`), test for statistical significance, and include a lagged inflation variable.

## 7. Repository Structure

```
├── data/
│   └── WHI_Inflation.csv            # raw dataset
├── figures/                         # charts exported by the analysis
├── output/
│   └── World_Happiness_Analysis.xlsx  # summary tables (rankings, regression, key findings)
├── WorldHappinessAnalysis.py        # full analysis (cleaning → EDA → regression)
└── README.md
```

## 8. How to Run

```bash
git clone https://github.com/<your-username>/world-happiness-analysis.git
cd world-happiness-analysis
pip install pandas numpy matplotlib seaborn scikit-learn openpyxl
python WorldHappinessAnalysis.py
```

## 9. Author

**Chu Đăng Quang**: Finance & Banking graduate, University of Economics and Business (VNU)
Email: quangcd.yec@gmail.com
