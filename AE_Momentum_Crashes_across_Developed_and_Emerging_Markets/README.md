
# Momentum Crashes across Developed and Emerging Markets

### International Evidence, Market States, Crash Risk, and Dynamic Momentum Strategies

**Research:** Xuan Feng  
**Research area:** Empirical Asset Pricing | Quantitative Investment | Momentum | Risk Management

[**Read the Research Paper (PDF)**](./AE_published_paper.pdf) | [**Run the Replication Notebook**](./Momentum_Crashes_International_Revision_OneClick.ipynb)

---

## 1. Project Overview

Momentum is one of the most widely documented investment strategies in financial markets. Its basic idea is simple: buy assets that have performed well in the past and sell assets that have performed poorly.

Although momentum can generate attractive long-term returns, it also has an important weakness: **momentum crashes**.

A momentum crash occurs when a strategy that has been profitable for an extended period suddenly suffers an unusually large loss. Such events are often associated with sharp market reversals, particularly when equity markets recover rapidly after prolonged downturns.

This project investigates momentum crash risk across **developed and emerging equity markets** using international Fama–French factor data.

The analysis addresses four main questions:

1. How do momentum returns and downside risks differ between developed and emerging markets?
2. Are momentum crashes associated with bear-market conditions, market volatility, and subsequent market rebounds?
3. Do the relationships between momentum and market states differ significantly across the two market groups?
4. Can volatility management and dynamic market-state timing improve momentum's risk-adjusted performance?

The project combines empirical asset-pricing models, crash-probability analysis, cross-market comparisons, and practical portfolio-management strategies.

The accompanying Jupyter Notebook reproduces the empirical tables and figures using publicly documented factor-series inputs.

---

## 2. Understanding Momentum Crashes

### 2.1 What is a momentum strategy?

A conventional momentum portfolio purchases past winners and sells past losers.

Its return can be expressed as:

$$
R_{WML,t}=R_{W,t}-R_{L,t}
$$

where:

- $R_{WML,t}$ is the winner-minus-loser (WML) momentum factor return.
- $R_{W,t}$ is the return on the winner portfolio.
- $R_{L,t}$ is the return on the loser portfolio.

For example, suppose:

- Past winners return $8\%$.
- Past losers return $3\%$.

The momentum portfolio earns:

$$
R_{WML}=8\%-3\%=5\%
$$

However, imagine a market recovery in which previously distressed stocks rebound rapidly:

- Past winners return $4\%$.
- Past losers return $19\%$.

Then:

$$
R_{WML}=4\%-19\%=-15\%
$$

Even though the overall stock market may be performing well, the momentum strategy suffers a substantial loss.

This is one of the central mechanisms associated with momentum crashes.

### 2.2 Why are market rebounds important?

Following a market downturn, previously underperforming stocks may experience particularly strong recoveries.

Because momentum portfolios are typically short past losers, such recoveries can cause significant losses on their short positions.

This creates a potentially asymmetric relationship between momentum returns and aggregate market movements.

Importantly, not every market rebound produces a momentum crash. The magnitude of the reversal, prevailing volatility, and previous market conditions may all matter.

---

## 3. Data and Sample

The empirical analysis uses monthly international Fama–French factor data for developed and emerging equity markets.

### Data files

| File | Description |
|---|---|
| `Developed_5_Factors.csv` | Developed-market five-factor returns and risk-free rate |
| `Developed_MOM_Factor.csv` | Developed-market momentum factor (WML) |
| `Emerging_5_Factors.csv` | Emerging-market five-factor returns and risk-free rate |
| `Emerging_MOM_Factor.csv` | Emerging-market momentum factor (WML) |

The five-factor datasets contain:

| Variable | Meaning |
|---|---|
| `Mkt-RF` | Market excess return |
| `SMB` | Small minus big (size factor) |
| `HML` | High minus low (value factor) |
| `RMW` | Robust minus weak (profitability factor) |
| `CMA` | Conservative minus aggressive (investment factor) |
| `RF` | Risk-free return |
| `WML` | Winner minus loser (momentum factor) |

### Sample coverage

| Market | Starting month | Ending month | Monthly observations |
|---|---|---|---:|
| Developed | November 1990 | April 2026 | 426 |
| Emerging | January 1990 | April 2026 | 436 |

These are the merged momentum-factor samples used in the replication Notebook.

Some regressions and trading strategies have shorter effective samples because they require historical observations or additional non-missing factor data.

### Data processing

The Notebook automatically:

1. Reads all four CSV files.
2. Removes non-monthly header and summary rows.
3. Converts `YYYYMM` dates into monthly timestamps.
4. Treats `-99.99` as missing data.
5. Converts returns from percentage points into decimals.
6. Merges the five-factor and momentum datasets by month.
7. Constructs market-state indicators and lagged risk measures.

All strategies and regressions operate on monthly data.

**Data source:** [Kenneth R. French Data Library](https://mba.tuck.dartmouth.edu/pages/faculty/ken.french/data_library.html)

---

## 4. Empirical Methodology

### 4.1 Identifying bear-market states

We first calculate the cumulative market return over the previous 24 months:

$$
R^{MKT}_{t-24:t-1}
=
\prod_{j=1}^{24}(1+R^{MKT}_{t-j})-1
$$

The market-state indicator is then:

$$
Bear_{t-1}
=
\begin{cases}
1, & R^{MKT}_{t-24:t-1}<0 \\
0, & R^{MKT}_{t-24:t-1}\geq 0
\end{cases}
$$

Here, $R^{MKT}$ is the total market return, calculated as `Mkt-RF + RF`.

In simple terms:

- $Bear_{t-1}=1$: the market has experienced a negative cumulative return over the preceding two years.
- $Bear_{t-1}=0$: the preceding two-year market return is non-negative.

The state classification uses only historical observations, avoiding future-information leakage.

### 4.2 Measuring market volatility

Historical market volatility is calculated using the preceding 12 monthly market excess returns:

$$
\sigma^{MKT}_{t-1}
=
Std(R^{MKT,excess}_{t-12},\ldots,R^{MKT,excess}_{t-1})
$$

Higher $\sigma^{MKT}_{t-1}$ indicates greater historical market uncertainty.

The interaction between the bear-market state and volatility allows the relationship between volatility and momentum returns to vary across market conditions.

### 4.3 Conditional momentum returns

We estimate:

$$
R_{WML,t}
=
\alpha
+
\beta_1 Bear_{t-1}
+
\beta_2 \sigma^{MKT}_{t-1}
+
\beta_3
\left(Bear_{t-1}\times\sigma^{MKT}_{t-1}\right)
+
\varepsilon_t
$$

The coefficients have intuitive interpretations:

- $\beta_1$: differences in momentum returns between bear and non-bear states.
- $\beta_2$: the relationship between market volatility and momentum returns outside bear states.
- $\beta_3$: how that volatility relationship changes following a bear-market state.

A negative $\beta_3$ suggests that elevated volatility is associated with weaker momentum performance when the preceding market environment is bearish.

Monthly OLS regressions use Newey–West heteroskedasticity-and-autocorrelation-consistent standard errors with six lags.

### 4.4 Measuring momentum crashes directly

An important distinction is that **a low momentum return is not automatically a momentum crash**.

The baseline analysis defines crashes using the market-specific unconditional fifth percentile of monthly WML returns:

$$
Crash_t
=
\mathbb{1}
\left(
R_{WML,t}\leq Q_{0.05}(R_{WML})
\right)
$$

Here, $Q_{0.05}$ represents the fifth percentile of the relevant market's momentum return distribution.

The Notebook also examines:

- An unconditional 10% lower-tail definition.
- A rolling 60-month 5% lower-tail threshold based only on previously observed returns.

The baseline unconditional threshold is a retrospective sample definition. The rolling threshold provides a more strictly ex-ante alternative.

### 4.5 Crash-probability model

Rather than examining average returns alone, we directly model the likelihood of extreme negative momentum returns.

The logistic model is:

$$
P(Crash_t=1\mid X_{t-1})
=
\frac{1}{1+\exp(-Z_t)}
$$

where:

$$
Z_t
=
\alpha
+
\beta_1 Bear_{t-1}
+
\beta_2 \sigma^{MKT}_{t-1}
+
\beta_3 Bear_{t-1}\sigma^{MKT}_{t-1}
$$

This model answers a practical question:

**Does the historical market environment help identify periods when momentum is more exposed to severe downside risk?**

The fitted probabilities describe statistical associations within the sample; they should not be interpreted as independently validated out-of-sample forecasts.

### 4.6 Testing the market-rebound mechanism

To examine how momentum behaves when markets rebound, the project estimates:

$$
\begin{aligned}
R_{WML,t}
={}&\alpha+\gamma_1R^{MKT,excess}_t+\gamma_2Bear_{t-1}\\
&+\gamma_3(Bear_{t-1}R^{MKT,excess}_t)\\
&+\gamma_4(Bear_{t-1}Up_tR^{MKT,excess}_t)
+\varepsilon_t
\end{aligned}
$$

where $Up_t=1$ when the contemporaneous market excess return is positive and zero otherwise.

The final interaction captures additional momentum sensitivity during positive market months following a bear-market state.

This specification helps distinguish a general market-return relationship from the more specific bear-market rebound mechanism.

---

## 5. Comparing Developed and Emerging Markets

The project estimates separate models for developed and emerging markets and then conducts a pooled regression with emerging-market interaction terms.

The pooled specification allows the main market-state coefficients to differ across the two groups.

A joint Wald test examines whether the three emerging-specific slope adjustments are simultaneously zero.

This is important because observing different coefficient estimates does not necessarily imply that the differences are statistically significant.

In the current replication, the joint Wald-test p-value is approximately $0.491$.

Therefore, the pooled test does not establish statistically significant differences across all three market-state slopes jointly.

This should not be interpreted as proof that the two markets have identical momentum dynamics.

---

## 6. Portfolio Strategies

Beyond examining the statistical properties of momentum crashes, the project evaluates whether simple portfolio-management rules can improve risk-adjusted performance.

Three approaches are considered.

### 6.1 Strategy A: Static momentum

The benchmark strategy maintains a constant exposure to the momentum factor:

$$
R^{Static}_t=R_{WML,t}
$$

This corresponds to a buy-and-hold exposure to the WML factor.

It provides the baseline against which more sophisticated approaches are evaluated.

### 6.2 Strategy B: Volatility-managed momentum

This strategy reduces momentum exposure when recent momentum volatility is high and increases exposure when volatility is relatively low.

The unnormalized exposure is:

$$
v_t=\frac{1}{(\sigma^{WML}_{t-1})^2}
$$

where $\sigma^{WML}_{t-1}$ is momentum volatility measured over the preceding 12 months.

To normalize the exposure without using future information, the Notebook applies an expanding historical average:

$$
c_t
=
\left(
\frac{1}{N_t}
\sum_{s\leq t}v_s
\right)^{-1}
$$

The final weight is:

$$
w^{VM}_t
=
\min\left(3,\max(0,c_tv_t)\right)
$$

The managed strategy return is:

$$
R^{VM}_t=w^{VM}_tR_{WML,t}
$$

**Intuition:** Take less momentum risk when recent momentum volatility is elevated.

Exposure is restricted to the interval $[0,3]$, meaning this rule does not deliberately reverse the momentum position.

### 6.3 Strategy C: Dynamic market-state momentum

The dynamic strategy goes beyond volatility scaling by incorporating information about expected momentum returns.

An expanding historical regression estimates:

$$
\widehat{\mu}_{WML,t}
=
\widehat{\alpha}_{t-1}
+
\widehat{\beta}_{1,t-1}Bear_{t-1}
+
\widehat{\beta}_{2,t-1}\sigma^{MKT}_{t-1}
+
\widehat{\beta}_{3,t-1}
Bear_{t-1}\sigma^{MKT}_{t-1}
$$

All regression coefficients are estimated using observations preceding month $t$.

The unnormalized dynamic position is:

$$
a_t
=
\frac{\widehat{\mu}_{WML,t}}
{(\sigma^{WML}_{t-1})^2}
$$

The scaling constant is calculated using the expanding mean absolute raw exposure:

$$
d_t
=
\left(
\frac{1}{N_t}
\sum_{s\leq t}|a_s|
\right)^{-1}
$$

The implemented position is:

$$
w^{Dynamic}_t
=
\max\left(-3,\min(3,d_ta_t)\right)
$$

The resulting return is:

$$
R^{Dynamic}_t
=
w^{Dynamic}_tR_{WML,t}
$$

Unlike simple volatility management, the dynamic strategy can take negative momentum exposure when predicted momentum returns are negative.

The Notebook requires at least 60 historical regression observations. Together with the initial 24-month market-state formation period, this results in an 84-month initial burn-in for the dynamic strategy.

**Important:** All strategy weights are calculated from historical data available at the portfolio-formation date.

---

## 7. Main Empirical Results

The following results are reproduced from the executed Notebook using the supplied datasets.

### 7.1 Static momentum performance

| Performance measure | Developed | Emerging |
|---|---:|---:|
| Annualized mean return | 6.85% | 10.24% |
| Annualized volatility | 12.85% | 10.45% |
| Sharpe ratio | 0.53 | 0.98 |
| Maximum drawdown | -43.61% | -37.26% |
| Monthly 5% VaR | -5.01% | -3.67% |
| Monthly 5% CVaR | -9.32% | -7.24% |

Both market groups exhibit substantial downside exposure despite positive average momentum returns.

The developed-market momentum factor shows a particularly severe historical monthly loss of approximately $-24.26\%$ in April 2009.

### 7.2 Crash frequencies and bear-market conditions

| Market | 5% crash frequency after bear states | 5% crash frequency after non-bear states |
|---|---:|---:|
| Developed | 16.67% | 2.98% |
| Emerging | 8.63% | 2.56% |

Crash frequencies are higher following bear-market states in both groups.

These descriptive results support the importance of investigating previous market conditions, while the regression analysis provides a more formal statistical assessment.

### 7.3 Strategy comparison

| Market | Strategy | Annualized return | Annualized volatility | Sharpe ratio | Maximum drawdown |
|---|---|---:|---:|---:|---:|
| Developed | Static | 6.85% | 12.85% | 0.53 | -43.61% |
| Developed | Volatility-managed | 6.52% | 7.54% | 0.86 | -10.74% |
| Developed | Dynamic | 10.03% | 12.21% | 0.82 | -26.18% |
| Emerging | Static | 10.24% | 10.45% | 0.98 | -37.26% |
| Emerging | Volatility-managed | 15.65% | 12.14% | 1.29 | -15.45% |
| Emerging | Dynamic | 11.31% | 9.24% | 1.22 | -11.66% |

The reported results suggest that:

- Volatility management can improve momentum's historical risk-adjusted performance.
- Dynamic market-state strategies can also produce attractive risk-adjusted outcomes.
- The best-performing approach varies across markets and performance measures.
- Reducing downside exposure does not necessarily require maximizing average returns.

**Comparability note:** The strategies have different starting dates because the managed rules require historical estimation windows. The table reproduces the Notebook's strategy-specific samples rather than imposing a common evaluation period.

The results also exclude transaction costs, financing costs, and other real-world implementation frictions.

---

## 8. Risk Measures

The project evaluates strategies using several complementary performance metrics.

### Annualized return

$$
\mu_{ann}=12\bar{R}
$$

### Annualized volatility

$$
\sigma_{ann}=\sqrt{12}\sigma_{monthly}
$$

### Sharpe ratio

For the factor-return series used in the Notebook:

$$
Sharpe=
\frac{\mu_{ann}}{\sigma_{ann}}
$$

### Value at Risk

The historical monthly 5% VaR is:

$$
VaR_{5\%}=Q_{0.05}(R_t)
$$

### Conditional Value at Risk

$$
CVaR_{5\%}
=
E[R_t\mid R_t\leq VaR_{5\%}]
$$

CVaR describes the average return in the worst 5% of observed months.

### Maximum drawdown

$$
MDD
=
\min_t
\left(
\frac{W_t}{\max_{s\leq t}W_s}-1
\right)
$$

where $W_t$ represents cumulative portfolio wealth.

These measures capture different dimensions of performance. Sharpe ratios summarize average return relative to volatility, while CVaR and maximum drawdown provide additional information about severe losses.

---

## 9. Repository Structure

```text
AE_Momentum_Crashes_across_Developed_and_Emerging_Markets/
│
├── AE_published_paper.pdf
│
├── Developed_5_Factors.csv
├── Developed_MOM_Factor.csv
├── Emerging_5_Factors.csv
├── Emerging_MOM_Factor.csv
│
├── Momentum_Crashes_International_Revision_OneClick.ipynb
│
└── README.md
```

### What does the Notebook generate?

Running the Notebook creates:

```text
revision_outputs/
│
├── tables/
│   ├── Table1_PanelA_FullSample.csv
│   ├── Table1_PanelB_Subperiod.csv
│   ├── Table2_Worst_Months.csv
│   ├── Table3_PanelA_Conditional_Return.csv
│   ├── Table3_PanelB_Crash_Logit.csv
│   ├── Table4_PanelA_Rebound.csv
│   ├── Table4_PanelB_Pooled.csv
│   ├── Table5_FF5_Alpha.csv
│   ├── Table6_PanelA_Strategy_FullSample.csv
│   ├── Table6_PanelB_Strategy_Subperiod.csv
│   ├── Table7_PanelA_Subperiod_Return.csv
│   ├── Table7_PanelB_Volatility_Windows.csv
│   ├── Table7_PanelC_Crash_Robustness.csv
│   └── Crash_Frequencies_By_State.csv
│
└── figures/
    ├── Figure1_Cumulative_Static_Momentum.png
    ├── Figure2_Static_Momentum_Drawdowns.png
    ├── Figure3_Fitted_Crash_Probability.png
    ├── Figure4_Developed_Strategies.png
    └── Figure5_Emerging_Strategies.png
```

The tables cover descriptive statistics, conditional regressions, crash-probability models, factor-adjusted performance, trading strategies, and robustness checks.

The figures illustrate cumulative performance, drawdowns, fitted crash probabilities, and comparisons among alternative momentum strategies.

---

## 10. How to Reproduce the Results

The analysis is implemented in Python using a single Jupyter Notebook.

No additional project-specific Python modules or custom classes are required.

### Step 1: Clone the repository

```bash
git clone https://github.com/XUAN-FENG9/Quant-Finance-and-Investment-Research.git
```

Navigate to the project directory:

```bash
cd Quant-Finance-and-Investment-Research/AE_Momentum_Crashes_across_Developed_and_Emerging_Markets
```

### Step 2: Install Python packages

Python 3 with the following packages is required:

```bash
pip install numpy pandas scipy matplotlib statsmodels notebook
```

### Step 3: Open the Notebook

```bash
jupyter notebook Momentum_Crashes_International_Revision_OneClick.ipynb
```

### Step 4: Set your data directory

In the first code cell, locate:

```python
DATA_DIR = r'C:\Users\HP16-af0022TU (D)\Desktop\Momentum crash_DEV_EM\data'
```

Replace it with the absolute path to the downloaded project folder.

For example:

```python
DATA_DIR = r'D:\Research\Momentum_Crashes'
```

All four CSV files must be placed directly inside this directory.

There is no need to create separate folders for developed and emerging markets.

### Step 5: Run all cells

In Jupyter Notebook, select:

**Kernel → Restart & Run All**

The Notebook will automatically:

1. Load and clean the factor data.
2. Merge developed and emerging market observations.
3. Calculate market-state and volatility variables.
4. Identify extreme momentum losses.
5. Estimate conditional-return and crash-probability models.
6. Test rebound dynamics and cross-market differences.
7. Estimate Fama–French five-factor regressions.
8. Construct volatility-managed and dynamic strategies.
9. Calculate risk and performance statistics.
10. Export the results into the `revision_outputs` directory.

Once the execution finishes, the Notebook performs reproducibility checks and prints the locations of the generated tables and figures.

---

## 11. Robustness Checks and Interpretation

The replication includes several robustness exercises.

**Alternative crash thresholds:** Crash-probability regressions are repeated using the unconditional 10% lower tail and a rolling historical 5% threshold.

**Alternative volatility windows:** The conditional-return model is re-estimated using 6-, 12-, and 24-month market-volatility measures.

**Subperiod stability:** The analysis compares observations before 2008 with those from 2010 onward, treating 2008–2009 as a separate crisis/transition period.

**Factor adjustment:** Momentum returns are evaluated using the Fama–French five-factor regression:

$$
R_{WML,t}
=
\alpha
+
\beta_M(Mkt-RF)_t
+
\beta_SSMB_t
+
\beta_HHML_t
+
\beta_RRMW_t
+
\beta_CCMA_t
+
\varepsilon_t
$$

**Cross-market testing:** Emerging-market interaction terms are assessed jointly using a Wald test.

### Important methodological considerations

The research should be interpreted with several qualifications:

- Conditional relationships may vary across historical periods.
- Extreme events are relatively rare, making crash-probability estimates sensitive to sample definitions.
- A significant in-sample relationship does not guarantee future predictive performance.
- Strategy results depend on leverage limits, historical estimation windows, and normalization rules.
- Reported returns exclude transaction costs, market-impact costs, financing expenses, and practical short-selling constraints.
- Strategy-specific observation periods differ; common-sample comparisons are advisable when evaluating incremental performance.

These considerations are particularly relevant when transferring academic factor results into practical portfolio-management applications.

---

## 12. Practical Applications

This project may be useful for:

**Quantitative researchers:** Studying nonlinear momentum risks, international factor behavior, and market-state-dependent return dynamics.

**Portfolio managers:** Evaluating how volatility scaling and adaptive positioning affect portfolio-level drawdowns and risk-adjusted returns.

**Risk managers:** Examining historical indicators associated with unusually large losses in momentum portfolios.

**Students and independent researchers:** Learning how to implement factor regressions, crash-probability models, expanding-window estimation, and reproducible backtests in Python.

The code is designed to be readable and relatively easy to modify for further empirical research.

---

## 13. Research Background

The analysis relates to several influential contributions to the momentum and volatility-management literature:

- Jegadeesh, N., & Titman, S. (1993). *Returns to Buying Winners and Selling Losers: Implications for Stock Market Efficiency.* Journal of Finance, 48(1), 65–91.
- Daniel, K., & Moskowitz, T. J. (2016). *Momentum Crashes.* Journal of Financial Economics, 122(2), 221–247.
- Barroso, P., & Santa-Clara, P. (2015). *Momentum Has Its Moments.* Journal of Financial Economics, 116(1), 111–120.
- Moreira, A., & Muir, T. (2017). *Volatility-Managed Portfolios.* Journal of Finance, 72(4), 1611–1644.
- Fama, E. F., & French, K. R. (2017). *International Tests of a Five-Factor Asset Pricing Model.* Journal of Financial Economics, 123(3), 441–463.

Readers interested in the complete empirical framework and detailed discussion should consult the [research paper](./AE_published_paper.pdf).

---

## 14. Citation and Reproducibility

If this repository supports your research, please cite the associated paper using the bibliographic information provided in the PDF.

The repository is intended to facilitate transparency, reproducibility, methodological discussion, and further research into momentum risk across international equity markets.

Researchers are welcome to adapt the analytical framework to other factor portfolios, asset classes, market groups, or portfolio-management questions, with appropriate attribution.

---

## Disclaimer

This repository is provided for academic research, educational purposes, and quantitative-finance methodology development.

The results are based on historical factor returns and simplified strategy implementations. They do not constitute investment advice or a representation of achievable live-trading performance.

**Author:** Xuan Feng  
**GitHub:** [XUAN-FENG9](https://github.com/XUAN-FENG9)
