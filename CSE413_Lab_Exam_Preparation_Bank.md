# CSE 413: Simulation and Modeling Lab — Exam Preparation Bank
**Course Teacher:** Audity Ghosh, Department of CSE, UITS  
**Exam Format:** 10 MCQs + 10 Short Questions (10 + 10 Marks)  
**Curated From:** Official Course Slides, Lecture PDFs, and Lab Code Templates in `resources/`

---

# TOPIC 1: Statistics Foundations, Data Types & Distributions
*(Sources: `Introduction to Statistics.pdf`, `uni_normal_distribution.ipynb`, `pyBasics.ipynb`)*

## 10 Most Important Short Questions & Answers

### 1. Differentiate between Discrete and Continuous quantitative data with examples from class slides.
* **Discrete Data:** Numerical values that are countable whole numbers with a finite range (e.g., number of students in a class = 30, number of books on a shelf = 12).
* **Continuous Data:** Numerical values that are measurable and can take infinite decimal values within an interval (e.g., person's height = 5.8 ft, body temperature = 36.5°C).

### 2. Explain the key distinction between Interval and Ratio measurement scales.
* **Interval Scale:** Has meaningful differences between values, but **no true zero** (e.g., 0°C does not mean "absence of temperature/heat"; 00:00 midnight does not mean "time ceases to exist").
* **Ratio Scale:** Has meaningful differences **and a true zero** (e.g., salary = $0 means zero income; weight = 0 kg means no physical mass).

### 3. Contrast Nominal and Ordinal categorical data.
* **Nominal:** Categorical labels without any inherent rank or order (e.g., eye color: Blue, Green; blood group: A, B, O; gender).
* **Ordinal:** Categorical data with a clear, meaningful hierarchy or order, but unequal or unmeasurable intervals (e.g., customer satisfaction: Satisfied > Neutral > Dissatisfied; education: High School < Bachelor < Master).

### 4. Why do we sample from a population rather than studying the entire population (N)?
Studying the entire population is often impossible because it is too large, financially expensive, time-consuming, and computationally heavy. A representative sample ($n$) saves cost and time while enabling accurate statistical inferences.

### 5. What is unbiased sampling, and give a good vs. bad example from the lecture.
* **Unbiased sampling:** Every member of the target population has a known, non-zero probability of selection, producing a representative subset.
* **Good Example:** Randomly selecting students across all university departments.
* **Bad Example:** Surveying only the top 10% CGPA students to represent overall student opinion.

### 6. What is a Gaussian (Normal) Distribution, and what are its key characteristics?
It is a continuous, symmetric, bell-shaped probability distribution where:
* The mean, median, and mode are all equal and located at the center.
* Data points cluster densely around the mean; probability tapers off symmetrically towards both tails.

### 7. Explain the difference between `np.random.rand()` and `np.random.randn()`.
* `np.random.rand()`: Generates random numbers from a **Uniform Distribution** over the continuous interval $[0, 1)$.
* `np.random.randn()`: Generates random numbers from a **Standard Normal Distribution** with mean $\mu = 0$ and standard deviation $\sigma = 1$.

### 8. Why is `np.random.seed()` essential in computational simulation experiments?
Computers generate *pseudorandom* sequences using deterministic algorithms. Setting `np.random.seed(x)` initializes the internal state of the generator, ensuring **experimental reproducibility**—the same code will produce the exact same sequence of values on every execution.

### 9. In Matplotlib histograms, what does setting `density=True` achieve?
Setting `density=True` normalizes the bin counts so that the total area under the histogram integrates/sums to 1. This converts the y-axis from raw frequencies into a probability density, allowing continuous probability density functions (like a normal curve overlay) to be compared directly.

### 10. Define Modeling vs. Simulation according to `pyBasics.ipynb`.
* **Modeling:** Creating a simplified conceptual or mathematical representation (blueprint, equations) of a real-world system.
* **Simulation:** Executing experiments with that model across time or stochastic trials to observe dynamic behavior under changing parameters.

## 10 Targeted MCQs with Explanations

1. **Temperature measured in degrees Celsius (0°C) is an example of which scale?**  
   - A) Nominal | B) Ordinal | C) Interval | D) Ratio  
   **Answer: C** *(Explanation: 0°C does not indicate total absence of heat; it is an arbitrary zero point).*

2. **Which measurement scale possesses a true absolute zero point?**  
   - A) Ratio | B) Interval | C) Ordinal | D) Nominal  
   **Answer: A** *(Explanation: Ratio scale has true zero, meaning complete absence of the property).*

3. **Customer satisfaction rated as Satisfied > Neutral > Dissatisfied belongs to:**  
   - A) Continuous data | B) Nominal data | C) Ordinal data | D) Interval data  
   **Answer: C** *(Explanation: Categories have clear rank, but interval distance is unmeasurable).*

4. **Eye color (Blue, Brown, Green) is:**  
   - A) Nominal qualitative | B) Ordinal qualitative | C) Continuous quantitative | D) Ratio quantitative  
   **Answer: A** *(Explanation: Descriptive labels with no inherent rank).*

5. **Which NumPy function draws samples from a Normal Distribution with customized mean ($\mu$) and standard deviation ($\sigma$)?**  
   - A) `np.random.rand()` | B) `np.random.normal(mean, std, size)` | C) `np.random.randn()` | D) `np.random.randint()`  
   **Answer: B** *(Explanation: `np.random.normal` accepts explicit mean, std, and size).*

6. **What is the mean and standard deviation of a Standard Normal distribution generated by `np.random.randn()`?**  
   - A) $\mu = 1, \sigma = 0$ | B) $\mu = 0, \sigma = 1$ | C) $\mu = 100, \sigma = 10$ | D) $\mu = 0, \sigma = 0$  
   **Answer: B** *(Explanation: By definition, standard normal has mean 0 and variance 1).*

7. **What does `np.random.permutation(10)` output?**  
   - A) 10 identical random floats | B) A shuffled array of integers from 0 to 9 | C) A 10x10 identity matrix | D) Random integers from 1 to 10  
   **Answer: B** *(Explanation: Random permutation shuffles the sequence 0 to 9).*

8. **In `plt.hist(data, bins=50, alpha=0.75)`, what does `alpha` represent?**  
   - A) Significance level of the test | B) Number of bins | C) Transparency (1.0 is opaque, 0.0 transparent) | D) Bin width  
   **Answer: C** *(Explanation: Controls plot element transparency).*

9. **If you execute code without `np.random.seed()` (or `np.random.seed(None)`), what happens?**  
   - A) It throws a runtime error | B) It defaults to seed 0 | C) It produces different random numbers every time it runs | D) It generates only zeros  
   **Answer: C** *(Explanation: OS entropy creates distinct random sequences on each run).*

10. **Which statement is TRUE regarding Population ($N$) and Sample ($n$)?**  
    - A) A sample is always larger than a population | B) Sample statistics are used to estimate unknown population parameters | C) Studying populations is always faster | D) Sample data is free from variance  
    **Answer: B** *(Explanation: Inferences regarding populations are made using sample statistics).*

---

# TOPIC 2: One-Sample $t$-Test
*(Sources: `T test.pptx.pdf`, `T_test.ipynb`)*

## 10 Most Important Short Questions & Answers

### 1. What is the fundamental objective of a One-Sample $t$-test?
To determine whether the mean of a single sample ($\bar{x}$) drawn from a population is statistically significantly different from a known or hypothesized population mean ($\mu_0$).

### 2. State the Null ($H_0$) and Alternative ($H_1$) hypotheses for the exam scores scenario in `T test.pptx.pdf`.
* **$H_0$:** $\mu = 70$ ("The class has the same average score as the school population; coffee/treatment has no real effect").
* **$H_1$:** $\mu \neq 70$ ("The class average score is significantly different from 70; coffee/treatment makes a measurable difference").

### 3. Write the mathematical formula for the one-sample $t$-statistic and define each symbol.
$$t = \frac{\bar{x} - \mu_0}{s / \sqrt{n}}$$
Where $\bar{x}$ is the sample mean, $\mu_0$ is the hypothesized population mean, $s$ is sample standard deviation ($\sqrt{\frac{\sum(x_i - \bar{x})^2}{n-1}}$), and $n$ is sample size.

### 4. What are the three core statistical assumptions required for a valid One-Sample $t$-test?
* The data variable is continuous (interval or ratio scale).
* The sample data follows an approximately normal distribution.
* Sample observations are independent of each other (random sampling).

### 5. What is the significance level ($\alpha$), and what does $\alpha = 0.05$ represent in decision theory?
$\alpha$ is the probability of committing a **Type I error** (rejecting the null hypothesis when it is actually true). $\alpha = 0.05$ means we accept a maximum 5% risk of incorrectly concluding there is a significant difference when none exists.

### 6. Explain what the $p$-value represents in a $t$-test.
The $p$-value is the probability of obtaining a test statistic as extreme as, or more extreme than, the observed value, assuming the null hypothesis ($H_0$) is strictly true.

### 7. What is the statistical decision rule using the $p$-value approach?
* If $p\text{-value} < \alpha$ (e.g., $0.05$): **Reject $H_0$** (Statistically significant difference).
* If $p\text{-value} \ge \alpha$: **Fail to reject $H_0$** (Insufficient evidence of a difference).

### 8. Why are the degrees of freedom defined as $df = n - 1$ in a one-sample $t$-test?
Because one parameter—the sample mean $\bar{x}$—must be estimated from the $n$ data points to calculate the sample variance. Once $\bar{x}$ is fixed, only $n - 1$ values are free to vary.

### 9. Write the exact Python SciPy function call used to execute a one-sample $t$-test.
```python
import scipy.stats as stats
t_statistic, p_value = stats.ttest_1samp(data, population_mean)
```

### 10. Solve the Student Work problem from `T_test.ipynb`: For `data = [5.2, 5.4, 5.1, 5.6, 5.3, 5.8]` and $\mu_0 = 5.5$, show sample mean and code interpretation.
* Sample mean $\bar{x} = \frac{5.2+5.4+5.1+5.6+5.3+5.8}{6} = 5.40$.
* SciPy computes $t \approx -0.98, p \approx 0.37$. Since $p > 0.05$, we fail to reject $H_0$; the class average is not significantly different from 5.5.

## 10 Targeted MCQs with Explanations

1. **In a one-sample $t$-test, the denominator $\frac{s}{\sqrt{n}}$ represents the:**  
   - A) Sample variance | B) Standard Error of the Mean (SEM) | C) Degrees of freedom | D) Pooled variance  
   **Answer: B** *(Explanation: SEM measures sample-to-sample variability of the mean).*

2. **For a sample of $n = 15$ students, the degrees of freedom in a one-sample $t$-test is:**  
   - A) 15 | B) 14 | C) 13 | D) 16  
   **Answer: B** *(Explanation: $df = n - 1 = 15 - 1 = 14$).*

3. **Rejecting a true null hypothesis is known as:**  
   - A) Type I error | B) Type II error | C) Power of test | D) Standard error  
   **Answer: A** *(Explanation: False positive = Type I error = $\alpha$).*

4. **If a test produces a $t$-statistic of 0.79 and a $p$-value of 0.4512 at $\alpha = 0.05$, the conclusion is:**  
   - A) Reject $H_0$ | B) Accept $H_1$ | C) Fail to reject $H_0$ | D) Data is non-normal  
   **Answer: C** *(Explanation: Since $p$-value $0.4512 > 0.05$, evidence is insufficient to reject $H_0$).*

5. **Which SciPy module contains the `ttest_1samp` function?**  
   - A) `scipy.linalg` | B) `scipy.optimize` | C) `scipy.stats` | D) `numpy.random`  
   **Answer: C** *(Explanation: Statistical testing functions reside in `scipy.stats`).*

6. **In `T_test.ipynb`, what does the red dashed line (`plt.axhline`) in the bar chart represent?**  
   - A) Sample standard deviation | B) Hypothesized population mean ($\mu_0$) | C) $p$-value threshold | D) Sample median  
   **Answer: B** *(Explanation: Plotted at $y = 70.0$ to compare sample mean visual bar against population mean).*

7. **If your sample size is $n = 10$, and all observations are identical to the hypothesized mean $\mu_0$, the $t$-statistic equals:**  
   - A) 1.0 | B) 0.0 | C) Undefined / infinity | D) -1.0  
   **Answer: B** *(Explanation: Numerator $\bar{x} - \mu_0 = 0$).*

8. **A one-sample $t$-test is appropriate when comparing:**  
   - A) Two experimental drug groups | B) One sample mean against a benchmark standard | C) Observed frequencies vs expected counts | D) Three group means  
   **Answer: B** *(Explanation: Evaluating a single sample against a known population benchmark).*

9. **The critical $t$-value for a two-tailed test at $\alpha = 0.05$ with $df = 9$ is computed in Python by:**  
   - A) `stats.t.cdf(0.05, 9)` | B) `stats.t.ppf(1 - 0.05/2, 9)` | C) `stats.t.pdf(0.05, 9)` | D) `stats.norm.ppf(0.05)`  
   **Answer: B** *(Explanation: `.ppf` computes percent point function; two-tailed splits $\alpha$ to $\alpha/2$).*

10. **If the calculated $t$-statistic is negative, it indicates:**  
    - A) A mathematical calculation error | B) The sample mean is less than the hypothesized population mean | C) The variance is negative | D) $H_0$ must automatically be rejected  
    **Answer: B** *(Explanation: $\bar{x} < \mu_0 \implies (\bar{x} - \mu_0) < 0$).*

---

# TOPIC 3: Two-Sample $t$-Test & Welch's Formulation
*(Sources: `Two_Sample_t_Test.pptx.pdf`, `SIM_Day_6 (1).ipynb`)*

## 10 Most Important Short Questions & Answers

### 1. What is the purpose of an independent Two-Sample $t$-test?
To compare the means of two independent groups (e.g., Diet Group vs Control Group; Drug A vs Amdocol) to evaluate whether the observed difference is statistically significant or merely due to random chance.

### 2. State $H_0$ and $H_1$ for the Two-Sample $t$-test.
* **$H_0$:** $\mu_1 = \mu_2$ (or $\mu_1 - \mu_2 = 0$, meaning no difference in population means).
* **$H_1$:** $\mu_1 \neq \mu_2$ (meaning a significant difference exists between group means).

### 3. What is Pooled Variance ($s_p^2$), and when is it valid to use?
Pooled variance is a weighted average of the two sample variances:
$$s_p^2 = \frac{(n_1 - 1)s_1^2 + (n_2 - 1)s_2^2}{n_1 + n_2 - 2}$$
It is valid **only** when both populations have equal variances ($\sigma_1^2 = \sigma_2^2$).

### 4. What are the degrees of freedom for the pooled two-sample $t$-test?
$$df = n_1 + n_2 - 2$$
(Since two means—$\bar{x}_1$ and $\bar{x}_2$—are estimated from the data).

### 5. Why must we specify `ddof=1` when computing sample variance in NumPy (`np.var(data, ddof=1)`)?
Because by default NumPy uses $ddof=0$ (population variance, dividing by $N$). For sample data, Bessel's correction requires dividing by $n-1$ ($ddof=1$) to yield an unbiased estimator of variance.

### 6. How does Welch’s $t$-test differ from Student's pooled $t$-test, and when should it be used?
* **Pooled $t$-test (`equal_var=True`):** Assumes $\sigma_1^2 = \sigma_2^2$ and pools variances.
* **Welch’s $t$-test (`equal_var=False`):** Does **not** assume equal variances; it computes standard error directly as $\sqrt{\frac{s_1^2}{n_1} + \frac{s_2^2}{n_2}}$ and uses fractional degrees of freedom. It should be used whenever group variances differ substantially.

### 7. Explain the decision rule based on the 95% Confidence Interval (CI) of the mean difference.
* **If CI includes 0** (e.g., $[-0.5, 2.3]$): The difference is **not statistically significant**; we fail to reject $H_0$.
* **If CI does NOT include 0** (e.g., $[1.5, 4.2]$): The difference **is statistically significant**; we reject $H_0$.

### 8. Write the formula for the Standard Error (SE) of the difference between two means using pooled variance.
$$\text{SE} = \sqrt{s_p^2 \left(\frac{1}{n_1} + \frac{1}{n_2}\right)}$$

### 9. Write the Python code to calculate the 95% Confidence Interval for the difference of means using SciPy.
```python
ci = stats.t.interval(0.95, df=df, loc=mean_diff, scale=std_err)
```

### 10. What happens to the two-sample $t$-statistic if a constant value $k$ is added to every observation in both groups?
It remains **completely unchanged**. Adding $k$ shifts both means by $+k$, leaving $\bar{x}_1 - \bar{x}_2$ unchanged, and adding a constant does not alter the sample variances ($s_1^2, s_2^2$). Thus $t$ and $p$ remain identical.

## 10 Targeted MCQs with Explanations

1. **In `stats.ttest_ind(data1, data2, equal_var=False)`, which test is executed?**  
   - A) Student's pooled $t$-test | B) Welch's $t$-test | C) Paired $t$-test | D) ANOVA  
   **Answer: B** *(Explanation: `equal_var=False` triggers Welch's unequal-variance formulation).*

2. **If $n_1 = 6$ and $n_2 = 6$, the degrees of freedom for the pooled $t$-test is:**  
   - A) 12 | B) 11 | C) 10 | D) 9  
   **Answer: C** *(Explanation: $df = n_1 + n_2 - 2 = 6 + 6 - 2 = 10$).*

3. **If the 95% confidence interval for $(\mu_1 - \mu_2)$ is $[-1.2, 3.8]$, at $\alpha = 0.05$ we:**  
   - A) Reject $H_0$ | B) Fail to reject $H_0$ | C) Conclude $\mu_1 > \mu_2$ | D) Reject $H_1$  
   **Answer: B** *(Explanation: 0 is inside the interval, meaning difference could plausibly be zero).*

4. **If the 95% confidence interval is $[2.1, 5.6]$, at $\alpha = 0.05$ we:**  
   - A) Reject $H_0$ | B) Fail to reject $H_0$ | C) Conclude the groups are identical | D) Accept $H_0$  
   **Answer: A** *(Explanation: 0 is excluded, confirming a significant positive difference).*

5. **In `SIM_Day_6 (1).ipynb`, sample variances are computed using:**  
   - A) `np.std(data)` | B) `np.var(data, ddof=1)` | C) `np.var(data, ddof=0)` | D) `stats.sem(data)`  
   **Answer: B** *(Explanation: `ddof=1` provides unbiased sample variance).*

6. **Independent samples in a two-sample $t$-test means:**  
   - A) Both groups must have equal sizes | B) Observations in one group do not influence observations in the other | C) The variances are equal to 0 | D) The data is perfectly normal  
   **Answer: B** *(Explanation: Data points in group 1 are unrelated to group 2).*

7. **When sample variances are unequal ($s_1^2 \neq s_2^2$), using the pooled $t$-test instead of Welch's $t$-test can:**  
   - A) Increase calculation speed without error | B) Lead to incorrect Type I error rates | C) Make the degrees of freedom infinite | D) Ensure perfect symmetry  
   **Answer: B** *(Explanation: Variance heterogeneity invalidates pooled $t$ assumptions).*

8. **In `stats.t.interval(0.95, df, loc=mean_diff, scale=std_err)`, what does `loc` represent?**  
   - A) Degrees of freedom | B) Center of the interval (point estimate: $\bar{x}_1 - \bar{x}_2$) | C) Standard deviation | D) Significance level  
   **Answer: B** *(Explanation: `loc` specifies the mean center of the interval).*

9. **If $p\text{-value} = 0.0004$ and $\alpha = 0.05$, the conclusion is:**  
   - A) Fail to reject $H_0$ | B) Statistically significant difference between groups | C) No significant difference | D) Re-sample the data  
   **Answer: B** *(Explanation: $p < 0.05$ warrants rejection of $H_0$).*

10. **In the blood pressure scenario (`Two_Sample_t_Test.pptx.pdf`), Drug A is concluded to be superior to Amdocol only if:**  
    - A) $p$-value $\ge 0.05$ | B) Mean difference is 0 | C) $p$-value $< 0.05$ and Blood Pressure reduction is significantly greater | D) Sample sizes are below 5  
    **Answer: C** *(Explanation: Statistical significance combined with directional clinical effect).*

---

# TOPIC 4: Chi-Square Goodness-of-Fit Test
*(Sources: `Chi Square.pptx`, `chi_sqr_test.ipynb`)*

## 10 Most Important Short Questions & Answers

### 1. What is the purpose of the Chi-Square Goodness-of-Fit test?
To evaluate whether observed categorical frequencies ($O_i$) significantly differ from theoretical expected frequencies ($E_i$) specified by a null hypothesis distribution (e.g., uniform, normal, or genetic ratios).

### 2. Write the mathematical formula for the Chi-Square statistic ($\chi^2$).
$$\chi^2 = \sum_{i=1}^{k} \frac{(O_i - E_i)^2}{E_i}$$
Where $O_i$ is observed frequency in category $i$, $E_i$ is expected frequency, and $k$ is total categories.

### 3. What are the degrees of freedom for a Chi-Square Goodness-of-Fit test?
$$df = k - 1 - p$$
Where $k$ is the number of categories and $p$ is the number of parameters estimated from the sample (in simple tests with no estimated parameters, $p = 0 \implies df = k - 1$).

### 4. State $H_0$ and $H_1$ for testing whether a coin is fair (flipped 100 times).
* **$H_0$:** The coin is fair ($P(\text{Heads}) = P(\text{Tails}) = 0.5$; expected count is 50 Heads, 50 Tails).
* **$H_1$:** The coin is not fair ($P(\text{Heads}) \neq P(\text{Tails})$).

### 5. How are expected frequencies calculated under a uniform distribution hypothesis in Python?
$$\text{Total} = \sum O_i, \quad E_i = \frac{\text{Total}}{k}$$
In Python: `expected = np.full_like(observed, np.sum(observed) / len(observed))`.

### 6. State the dual decision criteria (Critical Value vs. $p$-value) for the Chi-Square test.
* **Critical Value:** If $\chi^2_{\text{calculated}} > \chi^2_{\text{critical}}$, **Reject $H_0$**; otherwise, fail to reject.
* **$p$-value:** If $p < \alpha$ (0.05), **Reject $H_0$**; otherwise, fail to reject.

### 7. Write the Python code to compute the critical value of Chi-Square at $\alpha = 0.05$.
```python
critical_value = stats.chi2.ppf(1 - 0.05, df)
```

### 8. Solve Assignment 1 from `chi_sqr_test.ipynb` by hand: `observed = [45, 35, 25]` assuming a uniform distribution.
* Total $N = 45 + 35 + 25 = 105$. $k = 3 \implies E_i = \frac{105}{3} = 35$.
* $\chi^2 = \frac{(45 - 35)^2}{35} + \frac{(35 - 35)^2}{35} + \frac{(25 - 35)^2}{35} = \frac{100}{35} + 0 + \frac{100}{35} = \frac{200}{35} \approx \mathbf{5.71}$.
* $df = 3 - 1 = \mathbf{2}$.

### 9. Explain the bar-plotting trick used in `chi_sqr_test.ipynb` to compare observed vs expected frequencies side-by-side.
To prevent overlapping bars, observed bars are placed at positions `range(1, len(observed) + 1)`, while expected bars are shifted by adding width: `np.arange(1, len(observed) + 1) + 0.4`. `np.arange` is required because standard Python `range` does not allow float addition.

### 10. What is a general sample size rule of thumb for expected frequencies in a Chi-Square test?
Every expected frequency cell should ideally be at least 5 ($E_i \ge 5$). If expected frequencies are too small ($< 5$), the Chi-square approximation becomes inaccurate.

## 10 Targeted MCQs with Explanations

1. **In the Chi-Square formula $\sum \frac{(O - E)^2}{E}$, what does $O$ represent?**  
   - A) Overall mean | B) Observed frequency from real-world experiment | C) Expected theoretical count | D) Odds ratio  
   **Answer: B** *(Explanation: $O_i$ is the empirical tally in each category).*

2. **For a 6-sided die rolled 60 times, the expected frequency for each face under $H_0$ (fair die) is:**  
   - A) 6 | B) 10 | C) 60 | D) 12  
   **Answer: B** *(Explanation: $60 / 6 = 10$).*

3. **For a fair die test with 6 faces, what are the degrees of freedom?**  
   - A) 6 | B) 5 | C) 4 | D) 1  
   **Answer: B** *(Explanation: $k - 1 = 6 - 1 = 5$).*

4. **If $\chi^2_{\text{calculated}} = 1.00$ and $\chi^2_{\text{critical}} = 5.99$, what is the conclusion?**  
   - A) Reject $H_0$ | B) Fail to reject $H_0$; observed data matches expected distribution | C) Accept $H_1$ | D) Degrees of freedom is wrong  
   **Answer: B** *(Explanation: $\chi^2_{\text{calc}} < \chi^2_{\text{crit}} \implies$ Fail to reject).*

5. **What does the function `stats.chi2.ppf(0.95, 2)` return?**  
   - A) Probability of $\chi^2 = 0.95$ | B) Critical value at $\alpha = 0.05$ with $df = 2$ | C) $p$-value for $df = 2$ | D) Sample variance  
   **Answer: B** *(Explanation: Inverse of CDF at cumulative probability 0.95 gives critical cutoff $\approx 5.99$).*

6. **In `chi_sqr_test.ipynb`, `expected = np.full_like(observed, total / len(observed))` creates an array where:**  
   - A) Values increase linearly | B) Every element equals the uniform average | C) All values are zero | D) Only the first element is populated  
   **Answer: B** *(Explanation: `full_like` duplicates the uniform expected mean across all category slots).*

7. **If observed values exactly equal expected values ($O_i = E_i$ for all $i$), the Chi-Square statistic equals:**  
   - A) 1.0 | B) 0.0 | C) Degrees of freedom | D) Infinity  
   **Answer: B** *(Explanation: Numerator $\sum(O - E)^2 = 0$).*

8. **In a 2x2 contingency table (Chi-Square test of Independence), degrees of freedom is calculated as:**  
   - A) $r + c - 1$ | B) $(r - 1)(c - 1) = (2-1)(2-1) = 1$ | C) $r \times c = 4$ | D) $N - 1$  
   **Answer: B** *(Explanation: Contingency tables use $(r - 1)(c - 1)$).*

9. **Why do we square $(O_i - E_i)$ in the numerator of the Chi-Square formula?**  
   - A) To make larger differences stand out and eliminate negative signs | B) To calculate standard deviation | C) To normalize the data | D) To convert it into a $t$-distribution  
   **Answer: A** *(Explanation: Squaring prevents positive and negative differences from canceling out to zero).*

10. **A small $p$-value ($p < 0.05$) in a Chi-Square goodness-of-fit test indicates:**  
    - A) The observed data fits the expected model well | B) There is a statistically significant departure from the expected model | C) Sample size was too small | D) The die/coin is perfectly fair  
    **Answer: B** *(Explanation: Low $p$-value rejects $H_0$ of good fit).*

---

# TOPIC 5: Monte Carlo Simulation
*(Sources: `Monte_Carlo_Simulation_Basics.pptx.pdf`, `Day_4_Monte_Carlo_Simulation.ipynb`)*

## 10 Most Important Short Questions & Answers

### 1. Define Monte Carlo Simulation in simple terms according to the lecture slides.
Monte Carlo Simulation is a mathematical and computational technique that solves complex deterministic or probabilistic problems by running repeated random trials and averaging the observed outcomes.

### 2. Why is it called "Monte Carlo"?
It was named by scientists (including Stanislaw Ulam and John von Neumann) after the famous **Monte Carlo Casino in Monaco**, known for games of chance like roulette, dice, and cards, which rely on repeated random events.

### 3. State the 5-step procedure of Monte Carlo Simulation given in `Monte_Carlo_Simulation_Basics.pptx.pdf`.
1. Define the Problem.
2. Set Parameters and Input Ranges.
3. Generate Random Values.
4. Run the Simulation Many Times (Iterative Trials).
5. Analyze and Average the Results.

### 4. Explain how the value of $\pi$ is estimated using a circle inside a square.
* Consider a circle of radius $r = 1$ inscribed inside a $2 \times 2$ square.
* $\text{Area of circle} = \pi r^2 = \pi$; $\text{Area of square} = 2 \times 2 = 4$.
* The ratio of points falling inside the circle to total points dropped uniformly in the square is:
  $$\frac{\text{Points Inside}}{\text{Total Points}} \approx \frac{\text{Area of Circle}}{\text{Area of Square}} = \frac{\pi}{4} \implies \pi \approx 4 \times \frac{\text{Inside}}{\text{Total}}$$

### 5. In the quarter-circle simulation (`Day_4_...ipynb`), what is the mathematical condition for a point $(x, y)$ being inside the circle?
$$x^2 + y^2 \le 1$$
In Python: `if x*x + y*y <= 1: inside += 1` (where $x, y \in [0, 1)$).

### 6. What is the Law of Large Numbers, and how does it apply to Monte Carlo simulations?
The Law of Large Numbers states that as the number of randomly generated trials ($N$) increases, the sample average converges closer and closer to the true theoretical expected value. In Monte Carlo, increasing $N$ (e.g., from 1,000 to 50,000) reduces simulation variance and improves accuracy.

### 7. Write the complete Python code for estimating $\pi$ using Monte Carlo from `Day_4_Monte_Carlo_Simulation.ipynb`.
```python
import random

def estimate_pi(num_points):
    inside = 0
    for _ in range(num_points):
        x = random.random()
        y = random.random()
        if x * x + y * y <= 1:
            inside += 1
    pi = 4 * inside / num_points
    print("Estimated pi for", num_points, "points is:", pi)

estimate_pi(10000)
```

### 8. What are the primary advantages of Monte Carlo Simulation?
* Simple, intuitive, and highly powerful.
* Handles complex, non-linear, multi-dimensional systems where analytical formulas are intractable.
* Straightforward to code and parallelize.

### 9. What are the key limitations of Monte Carlo Simulation?
* Results are approximations, not exact analytical proofs.
* High accuracy requires large numbers of iterations (computationally intensive).
* Highly dependent on the quality of the pseudorandom number generator.

### 10. Give two real-world application areas of Monte Carlo mentioned in the slides.
* **Artificial Intelligence / Machine Learning:** Random hyperparameter tuning (e.g., randomly testing learning rates to find best validation accuracy).
* **Finance / Risk Analysis:** Forecasting stock prices, portfolio value-at-risk, and pricing complex derivative options under market uncertainty.

## 10 Targeted MCQs with Explanations

1. **Monte Carlo simulation is primarily based on:**  
   - A) Deterministic differential equations | B) Repeated random sampling and probability | C) Gradient descent | D) Matrix inversion  
   **Answer: B** *(Explanation: Relies on stochastic sampling to approximate solutions).*

2. **What happens to the Monte Carlo estimate of $\pi$ as `num_points` increases from 1,000 to 50,000?**  
   - A) It becomes less stable | B) It gets closer and more accurate to the true value of $\pi \approx 3.14159$ | C) It always equals 4.0 | D) It takes less execution time  
   **Answer: B** *(Explanation: More trials reduce stochastic error due to Law of Large Numbers).*

3. **In `Day_4_Monte_Carlo_Simulation.ipynb`, `random.random()` generates floats in the range:**  
   - A) $[0, 1)$ | B) $[-1, 1]$ | C) $[1, 10]$ | D) $[0, 4]$  
   **Answer: A** *(Explanation: Standard Python random generates continuous uniform $[0, 1)$).*

4. **If out of 10,000 random points, 7,850 fall inside the quarter-circle, the estimated $\pi$ is:**  
   - A) $7850 / 10000 = 0.785$ | B) $4 \times (7850 / 10000) = 3.140$ | C) $2 \times 0.785 = 1.57$ | D) $3.14159$  
   **Answer: B** *(Explanation: $\pi \approx 4 \times (\text{inside} / \text{total}) = 4 \times 0.785 = 3.140$).*

5. **The mathematical theorem that guarantees convergence in Monte Carlo simulations is the:**  
   - A) Law of Large Numbers | B) Pythagorean Theorem | C) Central Limit Law | D) Fermat's Little Theorem  
   **Answer: A** *(Explanation: Guarantees sample mean converges to expected value).*

6. **In a unit square $[0, 1] \times [0, 1]$, if a point has coordinates $x = 0.6, y = 0.8$, does it lie inside the quarter circle $x^2 + y^2 \le 1$?**  
   - A) Yes, because $0.6^2 + 0.8^2 = 0.36 + 0.64 = 1.00 \le 1$ | B) No, because $0.6 + 0.8 = 1.4 > 1$ | C) Yes, because $0.6 < 1$ | D) Undetermined  
   **Answer: A** *(Explanation: Boundary point satisfies $x^2 + y^2 \le 1$).*

7. **If $x = 0.7, y = 0.8$, does it lie inside the quarter circle?**  
   - A) Yes | B) No, because $0.49 + 0.64 = 1.13 > 1$ | C) Yes, because $x, y < 1$ | D) Undetermined  
   **Answer: B** *(Explanation: Distance from origin exceeds radius 1).*

8. **Which of the following is a drawback of Monte Carlo simulation?**  
   - A) Cannot handle random variables | B) Computationally expensive for high precision | C) Too difficult to code | D) Only works for $\pi$ estimation  
   **Answer: B** *(Explanation: Diminishing returns in error reduction require massive iteration counts).*

9. **Why is Monte Carlo preferred over analytical methods in complex physical systems?**  
   - A) Analytical equations may be impossible to solve in closed form | B) Computers cannot do calculus | C) Random numbers are always 100% exact | D) It requires no computer hardware  
   **Answer: A** *(Explanation: Simulating random paths solves intractable integrals).*

10. **In `Day_4_...ipynb`, why is `pi = 4 * inside / num_points` used instead of `inside / num_points`?**  
    - A) A square has 4 sides | B) The points are simulated in only 1 quadrant (quarter of a full circle) | C) Radius is 4 | D) To convert percentage to decimal  
    **Answer: B** *(Explanation: The simulation runs in quadrant 1, which represents $1/4$ of the circle area).*

---
