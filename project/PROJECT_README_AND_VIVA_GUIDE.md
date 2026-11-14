# CSE 413: Simulation and Modeling Lab — Comprehensive Project Guide & Viva Compendium
**Course Teacher:** Audity Ghosh, Lecturer, Department of CSE, UITS  
**Course Code:** CSE 413 | **Term:** 4/1 | **Credit Hour:** 3.00  
**Project Title:** Statistical Analysis and Monte Carlo Estimation of Extreme Hourly Bike-Sharing Demand  
**Audience:** Self-study, teammate onboarding, project defense preparation, and lab test viva.

---

## Table of Contents
1. [Executive Summary & Problem Statement](#1-executive-summary--problem-statement)
2. [Dataset Architecture & Column Governance](#2-dataset-architecture--column-governance)
3. [Stepwise Problem Breakdown & Problem-Solving Roadmap](#3-stepwise-problem-breakdown--problem-solving-roadmap)
4. [The Three Statistical Tests: Deep Dive & Decision Framework](#4-the-three-statistical-tests-deep-dive--decision-framework)
5. [Monte Carlo Simulation Engine & Tail Probability Estimation](#5-monte-carlo-simulation-engine--tail-probability-estimation)
6. [Imported Package Reference: Functions, Parameters & Return Values](#6-imported-package-reference-functions-parameters--return-values)
7. [Comprehensive Viva Questions & Answers Bank](#7-comprehensive-viva-questions--answers-bank)

---

## 1. Executive Summary & Problem Statement

### 1.1 The Operational Scenario
In modern smart cities, bike-sharing networks serve as critical micro-mobility infrastructure connecting transit hubs, universities, and commercial zones. However, transit operators face a severe operational vulnerability: **demand surges during peak hours and adverse weather events**. 

When demand dramatically spikes, stations run out of bikes, docking stations overflow, and riders are stranded. Conversely, when severe weather strikes, demand collapses, leaving idle assets depreciating across the city.

### 1.2 The Naive Mistake: Forecasting Averages (Expected Mean Demand)
Most traditional statistical forecasting projects attempt to answer:
> *"What is the expected average number of bikes rented at 5:00 PM on a clear Friday?"* (e.g., predicted mean $\hat{\lambda} = 413$ bikes).

**Why this is dangerous and fails in reality:**
1. **Real systems crash on extreme spikes, not on averages:** If capacity is sized for 450 bikes based on a mean expectation of 413, a surge of 600 or 800 bikes will immediately overwhelm the system.
2. **The False 50/50 Assumption:** Under a symmetric bell-curve (Normal distribution), people assume that if the mean is 413, there is virtually zero chance of exceeding 500 bikes.
3. **The Extreme Overdispersion Reality:** Real-world count data has a variance that is **174 times larger than the mean**! Because of this heavy right skew, an hour with an expected mean of 413 bikes actually has a **28.5% probability (nearly 1 in 3 chance)** of blowing past 500 bikes!

### 1.3 The Core Research Question
Instead of regression forecasting, our project asks:
> **"How does the probability of unusually high hourly bike-sharing demand, $P(\text{Demand} > C)$, change across weather conditions and working days, and how can we accurately estimate this probability using Monte Carlo simulation from a fitted predictive distribution validated on chronological holdouts?"**

---

## 2. Dataset Architecture & Column Governance

### 2.1 Selected Dataset & Source
* **Repository:** UCI Machine Learning Repository (Dataset ID: 275 - *Bike Sharing Dataset*).
* **Archive Content:** The UCI zip archive contains two files: `day.csv` (731 daily records) and `hour.csv` (17,379 hourly records).
* **Chosen Primary File:** `hour.csv` (17,379 rows, 17 columns) spanning 731 distinct calendar dates from 2011-01-01 to 2012-12-31.
* **Why `hour.csv` over `day.csv`?** Our research question explicitly targets **hourly demand spikes**. Only the hourly dataset captures peak rush hour surges (8 AM and 5 PM commute waves vs. midday leisure).

### 2.2 Exhaustive Column Dictionary & Governance Matrix

| Column Name | Data Type & Range | Description in Real World | Project Decision | Mathematical & Methodological Rationale |
|:---|:---|:---|:---:|:---|
| `instant` | Integer (1 to 17379) | Sequential row index | **Rejected** | Arbitrary record counter; carries zero physical or behavioral meaning. |
| `dteday` | Date (`YYYY-MM-DD`) | Calendar date (731 days) | **Retained** | Required for **chronological train/test splitting** and daily aggregation. |
| `season` | Categorical (1 to 4) | 1: Winter, 2: Spring, 3: Summer, 4: Fall | **Retained** | Controls for long-term climatic variations and seasonal shifts. |
| `yr` | Binary (0: 2011, 1: 2012) | Operating year | **Retained** | Critical for modeling **temporal drift** (demand grew significantly in 2012). |
| `mnth` | Integer (1 to 12) | Calendar month | **Retained** | Captures monthly cyclical demand fluctuations. |
| `hr` | Integer (0 to 23) | Hour of the day (0:00 to 23:00) | **Retained** | Recoded into operational time blocks (`hour_bin`: night, morning peak, midday, evening peak, late). |
| `holiday` | Binary (0 or 1) | Official public holiday | **Retained** | Indicates days where typical work commuting patterns vanish. |
| `weekday` | Integer (0 to 6) | Day of week (0: Sun to 6: Sat) | **Retained** | Differentiates weekend leisure patterns from weekday commutes. |
| `workingday` | Binary (0 or 1) | 1: neither weekend nor holiday; 0: weekend/holiday | **Retained** | Core stratification variable for hypothesis testing and Monte Carlo simulation. |
| `weathersit` | Categorical (1 to 4) | 1: Clear/Few clouds; 2: Mist/Cloudy; 3: Light Rain/Snow; 4: Heavy Rain/Ice/Storm | **Retained (Merged)** | Category 4 had only 3 observations in 2 years; deterministically merged into category 3 to maintain statistical cell validity. |
| `temp` | Float (Normalized 0 to 1) | Normalized temperature in Celsius: $(t - t_{\min}) / (t_{\max} - t_{\min})$ | **Retained** | Controls for continuous physical ambient temperature in count regression models. |
| `atemp` | Float (Normalized 0 to 1) | Normalized "feeling" temperature in Celsius | **Rejected** | Collinear with `temp` ($r > 0.98$). Retaining both causes variance inflation. |
| `hum` | Float (Normalized 0 to 1) | Normalized relative humidity | **Retained** | Continuous weather covariate in predictive model. |
| `windspeed` | Float (Normalized 0 to 1) | Normalized wind speed | **Retained** | Continuous atmospheric covariate influencing cycling comfort. |
| `casual` | Integer ($\ge 0$) | Count of rentals by unregistered users | **STRICTLY REJECTED** | **Target Leakage:** `casual + registered = cnt`. Including `casual` leaks the target directly into predictors. |
| `registered` | Integer ($\ge 0$) | Count of rentals by annual subscribers | **STRICTLY REJECTED** | **Target Leakage:** Directly forms the target `cnt`. Must never be used as a predictor. |
| `cnt` | Integer ($\ge 0$) | Total system-wide hourly bike rentals | **TARGET VARIABLE** | The primary metric of system load across the entire metropolitan area. |

---

## 3. Stepwise Problem Breakdown & Problem-Solving Roadmap

To systematically answer the research question, we decompose the challenge into 5 logical stages, applying concepts directly from our CSE 413 lab classes:

```
[Step 1: Ingestion & Governance]
└── Download UCI hour.csv -> Drop target leakage (casual, registered) -> Recode weather & hour_bin
    │
    ▼
[Step 2: Distribution Diagnosis (RQ1)]
└── Is cnt normally distributed? -> D'Agostino test, Skewness, Kurtosis, Q-Q plots
    └── Decision: REJECT H0 -> Overdispersed & strongly right-skewed count data
        │
        ▼
[Step 3: Temporal Aggregation & Mean Differences (RQ2)]
└── Does rental volume differ between working and non-working days?
    └── Aggregate 17,379 hours into 731 independent daily totals (break autocorrelation)
    └── Two-Sample Welch t-Test (equal_var=False) + Pooled Sensitivity Check (equal_var=True)
    └── Decision: FAIL TO REJECT H0 (p = 0.110) -> Daily volume is identical; hourly shape differs
        │
        ▼
[Step 4: Weather Association Analysis (RQ3)]
└── Is adverse weather associated with demand dropping below median?
    └── Form 3x2 contingency table -> Chi-Square Test of Independence + Cramér's V
    └── Decision: REJECT H0 (p = 5.0e-82) -> Weather significantly suppresses demand
        │
        ▼
[Step 5: Predictive Count Modeling & Monte Carlo Tail Simulation (RQ4)]
└── Fit Poisson vs Negative Binomial (NB2) -> Account for overdispersion
└── Draw N = 10,000 samples per stratum from fitted predictive distribution
└── Compute P(D > 500) and P(D > 800) with Monte Carlo Standard Error
└── Validate on unseen Chronological Holdout (final 20% of dates)
```

---

## 4. The Three Statistical Tests: Deep Dive & Decision Framework

In our lab course with Miss Audity Ghosh, every statistical evaluation requires three structured components:
1. **The Contextual Scenario & Scientific Hypotheses ($H_0$ vs. $H_1$)**
2. **The Test Statistic, Degrees of Freedom & $p$-Value Calculation**
3. **The Rigorous Statistical Decision:** Explicitly stating `Reject H0` or `Fail to Reject H0` at $\alpha = 0.05$.

---

### Test 1: Normality & Distribution Diagnostic (RQ1)
* **Mapped Lab Resource:** `uni_normal_distribution.ipynb` & `Introduction to Statistics.pdf`
* **Real-World Problem:** Classical regression and standard confidence intervals assume data follows a symmetric Gaussian bell-curve. Can we assume hourly bike demand is normally distributed?
* **Hypotheses:**
  * **$H_0$ (Null Hypothesis):** Hourly bike rental counts (`cnt`) follow a Normal Distribution.
  * **$H_1$ (Alternative Hypothesis):** Hourly bike rental counts do NOT follow a Normal Distribution.
* **Empirical Diagnostics:**
  * Mean ($\bar{x}$): $189.46$ bikes/hour
  * Variance ($s^2$): $32,917.60$
  * Standard Deviation ($s$): $181.43$
  * **Variance-to-Mean Ratio:** $\frac{s^2}{\bar{x}} = \frac{32,917.60}{189.46} \approx \mathbf{173.75}$ (Massive overdispersion; in Poisson it equals 1.0, in Normal variance is independent of mean).
  * **Skewness:** $+1.28$ (Strongly right-skewed; long tail of extreme demand).
  * **Kurtosis:** $+1.32$ (Leptokurtic; extreme events occur more frequently than in a Gaussian distribution).
  * **D’Agostino–Pearson Omnidirectional Test:** Test statistic $= 2,755.51, \quad p\text{-value} = \mathbf{0.0000} \ (< 10^{-300})$.
* **Statistical Decision:** **REJECT $H_0$.**
* **Conclusion:** Hourly demand violates normality completely. Standard linear Gaussian models are fundamentally invalid. Count-data models that allow for overdispersion (such as Negative Binomial) are mandatory.

---

### Test 2: Two-Sample Independent $t$-Test on Daily Demand (RQ2)
* **Mapped Lab Resource:** `Two_Sample_t_Test.pptx.pdf`, `SIM_Day_6 (1).ipynb`, `Simulation and Modeling Lab(7B1).pdf`
* **Real-World Problem:** Operators need to know if total daily system load differs between working days (commuters) and non-working days (weekends/holidays) so they can allocate maintenance crews and fleet rebalancing.
* **Why Test on Daily Aggregates (731 rows) instead of Hourly Rows (17,379 rows)?**
  * **The Autocorrelation Trap:** Hourly bike rentals violate the fundamental assumption of $t$-tests: **independence of observations**. What happens at 8:00 AM directly predicts 9:00 AM.
  * By summing rentals across each 24-hour day ($N = 731$ distinct calendar days), the daily totals are independent events.
* **Hypotheses:**
  * **$H_0$ (Null Hypothesis):** Mean daily rentals on working days equals mean daily rentals on non-working days:
    $$\mu_{\text{working}} = \mu_{\text{non-working}} \quad (\mu_{\text{working}} - \mu_{\text{non-working}} = 0)$$
  * **$H_1$ (Alternative Hypothesis):** Mean daily rentals on working days differ significantly from non-working days:
    $$\mu_{\text{working}} \neq \mu_{\text{non-working}}$$
* **Data Summary:**
  * Working Days ($n_1 = 500$ days): Mean $\bar{x}_1 = 4,584.82$, Standard Deviation $s_1 = 1,878.42$, Variance $s_1^2 = 3,528,477.5$
  * Non-Working Days ($n_2 = 231$ days): Mean $\bar{x}_2 = 4,330.17$, Standard Deviation $s_2 = 2,052.14$, Variance $s_2^2 = 4,211,273.7$
  * Mean Difference ($\bar{x}_1 - \bar{x}_2$): $+254.65$ rentals/day.
* **Primary Test (Welch’s $t$-Test — `equal_var=False`):**
  * Standard Error ($\text{SE}_{\text{Welch}}$): $\sqrt{\frac{s_1^2}{n_1} + \frac{s_2^2}{n_2}} = \sqrt{\frac{3528477.5}{500} + \frac{4211273.7}{231}} \approx 159.08$
  * Welch Degrees of Freedom ($df_{\text{Welch}}$): $413.94$
  * $t$-Statistic: $t = \frac{254.65}{159.08} = \mathbf{1.601}$
  * $p$-Value: $\mathbf{0.1101}$
  * 95% Confidence Interval for Difference: $[-57.94, +567.24]$
  * Standardized Effect Size (Cohen’s $d$): $\mathbf{0.132}$ (Classified as *negligible*).
* **Sensitivity Check (Student’s Pooled $t$-Test — `equal_var=True`):**
  * Pooled Variance ($s_p^2$): $\frac{(500-1)s_1^2 + (231-1)s_2^2}{500 + 231 - 2} = 3,744,304.5$
  * Pooled $df$: $n_1 + n_2 - 2 = 500 + 231 - 2 = 729$
  * Pooled $t$-Statistic: $\mathbf{1.654}, \quad p\text{-Value} = \mathbf{0.0985}$
  * Pooled 95% CI: $[-47.55, +556.85]$
* **Statistical Decision:** **FAIL TO REJECT $H_0$.**
* **Conclusion:** 
  1. Because $p = 0.1101 > 0.05$, there is **no statistically significant difference** in total daily rental volume between working days and non-working days.
  2. Because the 95% Confidence Interval $[-57.94, +567.24]$ **contains zero**, zero difference is a highly plausible true value.
  3. **Managerial Insight:** Working days and weekends see roughly the same total volume of riders. The difference is solely *temporal distribution* (commute spikes at 8 AM & 5 PM vs smooth midday weekend leisure).

---

### Test 3: Chi-Square Test of Independence on Weather (RQ3)
* **Mapped Lab Resource:** `Chi Square.pptx`, `chi_sqr_test.ipynb`, `Simulation and Modeling Lab(7B1).pdf`
* **Real-World Problem:** Does adverse weather (mist, rain, snow) significantly reduce the likelihood that hourly demand reaches above-median levels?
* **Metric Formulation:**
  * Full dataset median: $\text{Median}(\text{cnt}) = 142.0$ bikes/hour.
  * Binary variable: `above_median` = $1$ if $\text{cnt} > 142.0$, else $0$. (Strictly labeled *"above-median demand"*, not extreme).
  * Merged weather: Category 4 (severe storm/ice, $n=3$) merged into Category 3 (rain/snow).
* **Observed Contingency Table ($O_{ij}$):**
  | Weather Situation | Below / Equal Median ($0$) | Above Median ($1$) | Total Category Count | % Above Median |
  |:---|:---:|:---:|:---:|:---:|
  | **1: Clear / Few Clouds** | 5,295 | 6,118 | 11,413 | 53.61% |
  | **2: Mist / Cloudy** | 2,372 | 2,172 | 4,544 | 47.80% |
  | **3: Light Rain / Snow** | 1,041 | 381 | 1,422 | 26.79% |
  | **Total** | 8,708 | 8,671 | 17,379 | 49.89% |
* **Hypotheses:**
  * **$H_0$ (Null Hypothesis):** Weather category and whether demand is above-median are independent ($P(\text{Above Median} \mid \text{Weather}) = P(\text{Above Median})$).
  * **$H_1$ (Alternative Hypothesis):** Weather category and above-median demand are significantly associated.
* **Test Statistics & Expected Frequency Validation:**
  * Degrees of Freedom: $df = (\text{rows} - 1)(\text{cols} - 1) = (3 - 1)(2 - 1) = \mathbf{2}$.
  * Minimum Expected Cell Count ($E_{\min}$): $709.49$ (Far above the validity rule of thumb $E_i \ge 5$; 0 cells below 5).
  * Critical Value ($\alpha = 0.05, df = 2$): $\chi^2_{\text{crit}} = \mathbf{5.9915}$.
  * **Calculated Chi-Square Statistic:** $\chi^2 = \mathbf{374.402}$.
  * **$p$-Value:** $p = \mathbf{5.008 \times 10^{-82}}$.
  * **Effect Size (Cramér’s $V$):**
    $$V = \sqrt{\frac{\chi^2}{N \times \min(r-1, c-1)}} = \sqrt{\frac{374.402}{17,379 \times 1}} = \mathbf{0.1468}$$
* **Statistical Decision:** **REJECT $H_0$.**
* **Conclusion:** 
  1. Because $\chi^2 = 374.402 \gg 5.9915$ and $p < 10^{-80}$, weather is **highly significantly associated** with rental demand.
  2. Adverse weather (Rain/Snow) cuts the proportion of above-median hours in half (from $53.6\%$ down to $26.8\%$).
  3. However, Cramér’s $V = 0.147$ shows the strength of association is modest—hour of the day remains the primary operational driver of volume.

---

## 5. Monte Carlo Simulation Engine & Tail Probability Estimation

### 5.1 Why Count Modeling (Negative Binomial) Precedes Monte Carlo
In `Day_4_Monte_Carlo_Simulation.ipynb`, we estimated $\pi$ by sampling uniform points in a square. In our real-world project, before we can draw random numbers, we must know **what probability distribution to sample from**:
* **Why Poisson Fails:** Poisson regression assumes $\text{Var}(Y \mid X) = E(Y \mid X)$. But bike demand exhibits massive overdispersion ($\text{Var}/\text{Mean} \approx 174$).
* **The Solution — Negative Binomial (NB2):** NB2 introduces a dispersion parameter $\alpha$:
  $$\text{Var}(Y \mid X) = \mu + \alpha \mu^2$$
* **Statistical Model Selection:** A Likelihood Ratio (LR) test comparing Poisson to NB2 yielded an astronomical test statistic ($LR \approx 10^5$, halved boundary-corrected $p \approx 0.000$), decisively selecting the **Negative Binomial Model**.

### 5.2 The Monte Carlo Engine Architecture
For each operational scenario (stratum: `workingday × weather × hour_bin`):
1. **Intensity Estimation:** The fitted model predicts the conditional mean $\hat{\lambda}_i$ for each hour.
2. **Random Sampling:** We draw $N = 10,000$ independent random observations directly from the fitted Negative Binomial predictive distribution:
   $$D_{\text{sim}}^{(1)}, D_{\text{sim}}^{(2)}, \dots, D_{\text{sim}}^{(N)} \sim \text{NegBinomial}(\mu = \hat{\lambda}, \alpha)$$
3. **Threshold Evaluation:** We count how many simulated hours exceed an operational threshold $C$ (e.g., $C = 500$ or $C = 800$ bikes/hour):
   $$\hat{P}(D > C) = \frac{1}{N} \sum_{j=1}^{N} \mathbb{I}\left(D_{\text{sim}}^{(j)} > C\right) = \frac{\text{Count of draws exceeding } C}{N}$$
4. **Monte Carlo Standard Error:**
   $$\text{SE}_{\text{MC}} = \sqrt{\frac{\hat{p}(1 - \hat{p})}{N}}$$
   For $N = 10,000$ draws, simulation uncertainty $\text{SE}_{\text{MC}} < 0.005$ (less than half of one percent!).

### 5.3 The "Aha!" Discovery: Mean $\neq$ Median or Tail Risk
Consider the stratum `1|evening_peak|wd0` (Clear weather, weekend evening, 5 PM - 7 PM):
* The estimated mean demand is $\hat{\lambda} \approx 413.1$ bikes/hour.
* A naive engineer might think: *"413 is well below our 500-bike limit, so we are safe!"*
* **The Monte Carlo Result:** $P(D > 500) \approx \mathbf{0.285}$ ($28.5\%$).
* **Insight:** There is nearly a **1-in-3 chance** of demand exceeding 500 bikes, even though the expected average is only 413. This proves why Monte Carlo estimation from full predictive distributions is essential for capacity planning.

---

## 6. Imported Package Reference: Functions, Parameters & Return Values

To ensure you can defend the implementation in front of faculty, here is the exact breakdown of every major function called from `scipy.stats`, `statsmodels`, `numpy`, and `matplotlib`.

---

### 6.1 `scipy.stats` Functions

#### 1. `stats.normaltest(a, axis=0, nan_policy='propagate')`
* **Used In:** Step 4 (Distribution Diagnosis).
* **Why Used:** Implements the **D’Agostino and Pearson omnibus test** for normality, combining skewness and kurtosis tests.
* **Parameters Passed:** `cnt` (1D NumPy array of rental counts).
* **Return Values:** 
  * `statistic` (float): $s^2 + k^2$, $\chi^2$-distributed with 2 degrees of freedom.
  * `pvalue` (float): Two-sided chi-squared probability. If $p < 0.05$, reject normality.

#### 2. `stats.ttest_1samp(a, popmean, alternative='two-sided')`
* **Used In:** Lab template `T_test.ipynb` & Master Compendium Module 3.
* **Why Used:** Tests if a single sample mean differs from a known benchmark.
* **Parameters Passed:**
  * `a`: Array of sample data (e.g., `[65, 72, 68, ...]` or `student_data`).
  * `popmean`: Known benchmark value $\mu_0$ (e.g., $70$ or $5.5$).
* **Return Values:**
  * `statistic` (float): The calculated $t$-value: $\frac{\bar{x} - \mu_0}{s/\sqrt{n}}$.
  * `pvalue` (float): Two-tailed probability of observing $|t|$ under $H_0$.

#### 3. `stats.ttest_ind(a, b, equal_var=True, alternative='two-sided')`
* **Used In:** Step 7 (Two-Sample $t$-test on Daily Volume).
* **Why Used:** Compares the means of two independent groups (`working` vs `nonworking` daily totals).
* **Parameters Passed:**
  * `a`: Sample 1 array (`working` daily rentals, $n_1=500$).
  * `b`: Sample 2 array (`nonworking` daily rentals, $n_2=231$).
  * `equal_var`: **Boolean flag**.
    * `equal_var=False`: Executes **Welch’s $t$-test** (does not assume equal variances).
    * `equal_var=True`: Executes **Student’s pooled $t$-test** (assumes equal variances).
* **Return Values:**
  * `statistic` (float): The calculated $t$-statistic.
  * `pvalue` (float): Two-tailed $p$-value.

#### 4. `stats.t.interval(confidence, df, loc=0, scale=1)`
* **Used In:** Step 7 and `SIM_Day_6 (1).ipynb`.
* **Why Used:** Constructs confidence intervals for the difference of two means.
* **Parameters Passed:**
  * `confidence`: Confidence coefficient (e.g., $0.95$ for 95% CI).
  * `df`: Degrees of freedom ($df_{\text{pooled}} = n_1 + n_2 - 2$ or $df_{\text{Welch}}$).
  * `loc`: Center of the interval (the point estimate: $\bar{x}_1 - \bar{x}_2$).
  * `scale`: Standard error of the difference ($\text{SE}$).
* **Return Values:** `(lower_bound, upper_bound)` tuple of confidence limits.

#### 5. `stats.chi2_contingency(observed, correction=False)`
* **Used In:** Step 11 (Weather Independence Test).
* **Why Used:** Computes the Chi-square test of independence on a 2D contingency table.
* **Parameters Passed:**
  * `observed`: $3 \times 2$ matrix/array of observed frequency counts.
  * `correction`: Boolean. Set to `False` to prevent Yates' continuity correction on large tables.
* **Return Values:**
  * `chi2` (float): The calculated $\chi^2$ statistic: $\sum \frac{(O - E)^2}{E}$.
  * `p` (float): The $p$-value of the test.
  * `dof` (int): Degrees of freedom: $(r - 1)(c - 1)$.
  * `expected` (2D ndarray): Expected theoretical frequency table under $H_0$.

#### 6. `stats.chi2.ppf(q, df)` and `stats.t.ppf(q, df)`
* **Used In:** Step 7, Step 11, and `chi_sqr_test.ipynb`.
* **Why Used:** **Percent Point Function** (the inverse of Cumulative Distribution Function, CDF). Computes the exact critical cutoff threshold.
* **Parameters Passed:**
  * `q`: Cumulative probability ($1 - \alpha$ for one-tailed, $1 - \alpha/2$ for two-tailed).
  * `df`: Degrees of freedom.
* **Return Values:** The critical value (e.g., `stats.chi2.ppf(0.95, df=2)` returns $5.9915$).

---

### 6.2 `statsmodels` Functions

#### 1. `smf.glm(formula, data, family=sm.families.NegativeBinomial(alpha=...))`
* **Used In:** Step 13 (Count Model Fitting).
* **Why Used:** Fits Generalized Linear Models (GLM) for count data (Poisson or Negative Binomial).
* **Parameters Passed:**
  * `formula`: Patsy formula string, e.g., `"cnt ~ C(workingday) + C(weather_merged) + C(hour_bin)"`.
  * `data`: Training DataFrame.
  * `family`: Exponential dispersion family (Poisson or Negative Binomial with dispersion $\alpha$).
* **Return Values:** `GLMResultsWrapper` object providing `.fit()`, `.summary()`, `.aic`, and `.predict()`.

---

### 6.3 `numpy` Functions

#### 1. `np.var(a, ddof=1)` and `np.std(a, ddof=1)`
* **Why Used:** Calculates variance and standard deviation.
* **Crucial Parameter `ddof=1`:** Delta Degrees of Freedom. By default, `ddof=0` calculates *population variance* (dividing by $N$). Setting `ddof=1` applies **Bessel's correction** (dividing by $n - 1$), providing an unbiased estimator for sample data.

#### 2. `np.full_like(a, fill_value)`
* **Used In:** `chi_sqr_test.ipynb` (Step 2).
* **Why Used:** Creates an array with the same shape and type as `a`, filled with `fill_value` (used to generate uniform expected frequencies: $E_i = \text{Total} / k$).

---

## 7. Comprehensive Viva Questions & Answers Bank

This bank contains the exact questions asked during lab exams, Class Performance (CP1 & CP2), and formal project defenses by Miss Audity Ghosh.

---

### Category A: Dataset & Problem Formulation

#### Q1. Why is this project not just a simple linear regression or demand forecasting model?
* **Answer:** Linear regression attempts to predict the conditional mean $\hat{y}$ (the expected average). Our project focuses on **operational tail risk estimation**: calculating the exact probability that hourly demand exceeds a critical system limit ($P(D > C)$) using Monte Carlo draws from a fitted predictive distribution validated on chronological holdouts.

#### Q2. Why did you choose `hour.csv` instead of `day.csv`?
* **Answer:** `day.csv` averages out all intra-day fluctuations. The operational crisis of bike shortages occurs during specific rush hours (e.g., 8:00–9:00 AM and 5:00–6:00 PM). Only `hour.csv` captures these acute hourly surges.

#### Q3. What is "target leakage", and why did you remove the `casual` and `registered` columns?
* **Answer:** In the dataset, total rentals is literally defined as `cnt = casual + registered`. If `casual` or `registered` are included as predictors, the model would achieve artificial 100% accuracy by simple addition, which is impossible in real-world forward operations.

#### Q4. Why did you split the dataset chronologically (by calendar date) instead of a random train/test split?
* **Answer:** Bike rentals are a **time-series process** with strong autocorrelation between consecutive hours and days. A random train/test shuffle would place 8:00 AM in the test set while training on 7:00 AM and 9:00 AM of the same day, causing massive data leakage and giving artificially inflated performance. A chronological holdout tests the model on true unseen future dates.

---

### Category B: Statistical Tests & Hypotheses

#### Q5. What were your hypotheses for the Normality test, and what was your decision?
* **Answer:**
  * $H_0$: Hourly demand follows a Normal Distribution.
  * $H_1$: Hourly demand does not follow a Normal Distribution.
  * **Decision:** **Reject $H_0$** ($p \approx 0.000$, Skewness = $1.28$, Variance/Mean ratio = $174$). Hourly count data is strongly right-skewed and overdispersed.

#### Q6. What were your hypotheses for the Two-Sample $t$-test, and what was your decision?
* **Answer:**
  * $H_0$: Mean daily rentals on working days equals mean daily rentals on non-working days ($\mu_1 = \mu_2$).
  * $H_1$: Mean daily rentals differ significantly ($\mu_1 \neq \mu_2$).
  * **Decision:** **Fail to Reject $H_0$** ($p = 0.1101 > 0.05$; 95% CI $[-57.94, +567.24]$ includes 0). Total daily volume is practically the same across working and non-working days.

#### Q7. Why did you use Welch’s $t$-test instead of Student's pooled $t$-test as your primary test?
* **Answer:** Student’s pooled $t$-test assumes both groups have equal population variances ($\sigma_1^2 = \sigma_2^2$). In our data, the non-working day variance is $1.19$ times larger than working day variance ($s_2^2 = 4.21 \times 10^6$ vs $s_1^2 = 3.53 \times 10^6$). Welch’s $t$-test is robust to variance heterogeneity and avoids inflated Type I errors.

#### Q8. If we add a constant $d$ to all observations in both groups (as in `Simulation and Modeling Lab(7B1).pdf` where $d = \text{student ID}$), what happens to the $t$-statistic?
* **Answer:** The $t$-statistic and $p$-value remain **completely unchanged**. Adding a constant $d$ shifts both sample means by $+d$, so the difference $(\bar{x}_1 + d) - (\bar{x}_2 + d) = \bar{x}_1 - \bar{x}_2$ is identical. Furthermore, adding a constant does not alter sample variance ($s^2$).

#### Q9. What were your hypotheses for the Chi-Square test, and what was your decision?
* **Answer:**
  * $H_0$: Weather situation and above-median demand are independent.
  * $H_1$: Weather situation and above-median demand are significantly associated.
  * **Decision:** **Reject $H_0$** ($\chi^2 = 374.402 > \chi^2_{\text{crit}} = 5.9915, p = 5.0 \times 10^{-82}$). Bad weather drastically suppresses rental volume.

#### Q10. What does Cramér’s $V$ tell us in the Chi-Square test?
* **Answer:** While the $\chi^2$ statistic tells us whether an association exists ($p < 0.05$), it scales with sample size. **Cramér’s $V$ measures the strength of the association** on a 0 to 1 scale. Our $V = 0.147$ indicates a statistically significant but modest effect, proving that while weather matters, time of day (rush hours) remains the dominant operational driver.

---

### Category C: Monte Carlo Simulation & Code Mechanics

#### Q11. How does your Monte Carlo simulation relate to the $\pi$ estimation in `Day_4_Monte_Carlo_Simulation.ipynb`?
* **Answer:** In the lab class, Monte Carlo estimated $\pi$ by repeatedly generating uniform random coordinates $(x, y)$ inside a square and evaluating a boundary condition ($x^2 + y^2 \le 1$). In our project, we repeatedly draw random samples from the fitted Negative Binomial distribution and evaluate an operational threshold condition ($D_{\text{sim}} > C$). In both cases, the proportion of successful trials estimates the unknown probability.

#### Q12. Why do you use $N = 10,000$ draws in Monte Carlo, and what does the Standard Error measure?
* **Answer:** According to the **Law of Large Numbers**, more iterations yield higher accuracy. With $N = 10,000$, the Monte Carlo Standard Error $\text{SE} = \sqrt{\frac{p(1-p)}{N}} \le \sqrt{\frac{0.25}{10,000}} = 0.005$ ($\le 0.5\%$). This SE measures **simulation noise/randomness only**; it does not measure real-world parameter uncertainty.

#### Q13. In NumPy, what is the difference between `np.var(x)` and `np.var(x, ddof=1)`?
* **Answer:** By default, `np.var(x)` uses `ddof=0`, dividing by $N$ (calculating population variance). For sample data, Bessel's correction requires dividing by $n - 1$, which is achieved by setting `ddof=1` to yield an **unbiased estimator** of sample variance.

#### Q14. What does the `.ppf()` function in SciPy do?
* **Answer:** `.ppf()` stands for **Percent Point Function**, which is the inverse of the Cumulative Distribution Function (CDF). Given a probability $1 - \alpha$, it calculates the exact critical value cutoff (e.g., `stats.chi2.ppf(0.95, df=2)` returns the critical value $5.9915$).

---
**End of Project Guide & Viva Compendium.**
