# Momentum Crashes across Developed and Emerging Markets

**International Evidence, Market-State Dependence, and Risk-Managed Momentum Strategies**

[**Published article (Applied Economics)**](https://doi.org/10.1080/00036846.2026.2749703) · [**Paper PDF**](./AE_published_paper.pdf) · [**Replication Notebook**](./Momentum_Crashes_International_Revision_OneClick.ipynb)

## 1. Overview

Momentum is a familiar investment strategy: buy assets that have recently performed well and sell assets that have performed poorly. Over long periods, this strategy can earn positive returns. However, it occasionally suffers unusually large losses, known as **momentum crashes**.

This project studies momentum crash risk in **developed and emerging equity markets**. It asks four practical questions:

1. How do momentum returns and downside risks compare across the two market groups?
2. Are crashes associated with earlier bear markets, elevated volatility, and sharp market rebounds?
3. Are the relationships between momentum and market conditions statistically different across developed and emerging markets?
4. Can volatility management or dynamic market-state timing improve momentum's risk-adjusted performance?

The repository contains the research paper, four factor-data files, and a single Jupyter Notebook that reproduces the empirical analysis and exports its tables and figures.

**Main idea:** A strategy can have attractive average returns but still be exposed to exceptionally severe losses. Understanding *when* these losses occur is as important as measuring average profitability.

## 2. Intuition: Why Does Momentum Crash?

A long-short momentum portfolio buys past winners and shorts past losers. Its return is

$$
R_{WML,t}=R_{W,t}-R_{L,t}.
$$

Here, $R_{W,t}$ is the winner-portfolio return, $R_{L,t}$ is the loser-portfolio return, and $R_{WML,t}$ is the **Winners Minus Losers (WML)** factor return.

Imagine that past winners rise by 8% while past losers rise by 3%. Momentum earns 5%. Now imagine a strong market rebound: past winners rise by 4%, but previously distressed losers jump by 19%. The same long-short portfolio loses 15%.

This helps explain an important momentum-crash mechanism:

- During prolonged market declines, past losers can become particularly distressed.
- When the market rebounds sharply, these previously distressed stocks may recover faster than past winners.
- A momentum portfolio that is short those stocks can incur large losses, even when the overall equity market rises.

Not every rebound produces a crash. The empirical analysis therefore considers the **preceding market state**, **historical volatility**, and **contemporaneous market rebounds** separately.

## 3. Data

The project uses monthly international equity-factor series supplied in the repository. The variables follow the Fama–French factor framework; the momentum factor is provided as WML rather than constructed from individual stocks.

| File | Contents |
|---|---|
| `Developed_5_Factors.csv` | Developed-market five factors and risk-free rate |
| `Developed_MOM_Factor.csv` | Developed-market momentum (WML) |
| `Emerging_5_Factors.csv` | Emerging-market five factors and risk-free rate |
| `Emerging_MOM_Factor.csv` | Emerging-market momentum (WML) |

The factor variables are `Mkt-RF` (market excess return), `SMB` (size), `HML` (value), `RMW` (profitability), `CMA` (investment), `RF` (risk-free return), and `WML` (momentum).

### Sample coverage

| Market group | First momentum month | Last month | Observations |
|---|---|---|---:|
| Developed | November 1990 | April 2026 | 426 |
| Emerging | January 1990 | April 2026 | 436 |

The Notebook joins the five-factor and momentum files by month, keeps valid monthly `YYYYMM` dates, converts reported percentage returns to decimal returns, and treats `-99.99` as missing. Individual regressions may use fewer observations because of required lagged variables or missing factor values.

For background on international factor definitions, see the [Kenneth R. French Data Library](https://mba.tuck.dartmouth.edu/pages/faculty/ken.french/data_library.html).

## 4. Identifying Market Conditions and Crashes

### 4.1 Previous bear-market state

The analysis first calculates the **cumulative total market return over the previous 24 months**, excluding the current month:

$$
R^{MKT}_{t-24:t-1}=\prod_{j=1}^{24}(1+R^{MKT}_{t-j})-1.
$$

The market-state indicator is

$$
Bear_{t-1}=\begin{cases}
1, & R^{MKT}_{t-24:t-1}<0,\\
0, & R^{MKT}_{t-24:t-1}\geq 0.
\end{cases}
$$

The total market return is computed from `Mkt-RF + RF`. Thus, $Bear_{t-1}=1$ means that the market had a negative cumulative return over the previous two years. **Only past months are used to construct this state.**

### 4.2 Lagged market volatility

The baseline volatility indicator is the sample standard deviation of the previous 12 monthly **market excess returns**:

$$
\sigma_{M,t-1} = \mathrm{SD}(R_{M,t-12}^{ex}, \ldots, R_{M,t-1}^{ex})
$$

The historical window avoids incorporating the return being explained. Robustness tests repeat the analysis using 6- and 24-month volatility windows.

### 4.3 Defining a momentum crash

The baseline crash indicator equals one if WML is among the **worst 5% of monthly returns** within its market group:

$$
Crash_{0.05,t} = \mathrm{I}[R_{WML,t} \leq Q_{0.05}(R_{WML})]
$$

Here, $Q_{0.05}$ denotes the fifth percentile of the full-sample momentum-return distribution. The Notebook also considers the worst 10% and a rolling 60-month fifth-percentile threshold based entirely on information from months before $t$.

**Interpretation matters:** The full-sample crash threshold is useful for *retrospectively identifying historical crashes*, not for a fully ex-ante trading rule. The rolling definition is a stricter check against future-information use in crash classification.

## 5. Econometric Analysis

### 5.1 Conditional momentum returns

The baseline regression relates current momentum returns to past market conditions:

$$
R_{WML,t}=\alpha+\beta_1 Bear_{t-1}+\beta_2\sigma^{MKT}_{t-1}+\beta_3\left(Bear_{t-1}\times\sigma^{MKT}_{t-1}\right)+\varepsilon_t.
$$

- $\beta_1$ captures the bear-state difference when the volatility term is zero; interpreting it in isolation requires care.
- $\beta_2$ is the association between volatility and WML returns outside bear states.
- $\beta_3$ shows how that volatility association changes after a bear market.

The OLS regressions use **Newey–West/HAC standard errors with six lags**.

### 5.2 Probability of an extreme momentum loss

An average-return regression is not the same as a crash-probability model. To study the probability of a large loss directly, the Notebook estimates a logistic specification:

$$
P(Crash_{0.05,t}=1 \mid X_{t-1}) = \frac{1}{1+\exp(-z_t)}
$$

with

$$
z_t=\alpha+\beta_1 Bear_{t-1}+\beta_2\sigma^{MKT}_{t-1}+\beta_3 Bear_{t-1}\sigma^{MKT}_{t-1}.
$$

This asks whether the historical state is associated with a higher probability of an extreme monthly momentum loss. The fitted probabilities are **in-sample statistical estimates**, not verified out-of-sample forecasts.

### 5.3 Market-rebound mechanism

A separate regression tests whether momentum behaves differently when the market rises following a bearish period:

$$
\begin{aligned}
R_{WML,t}={}&\alpha+\gamma_1R^{MKT,excess}_t+\gamma_2Bear_{t-1}\\
&+\gamma_3\left(Bear_{t-1}R^{MKT,excess}_t\right)\\
&+\gamma_4\left(Bear_{t-1}Up_tR^{MKT,excess}_t\right)+\varepsilon_t,
\end{aligned}
$$

where $Up_t=1$ if the current market excess return is positive, and $Up_t=0$ otherwise. Unlike the *lagged* state variables, the contemporaneous market return is used here to **explain realized momentum returns**, not to form an investable trading signal.

### 5.4 Developed versus emerging markets

The Notebook estimates separate models and a pooled regression with emerging-market interactions. A **joint Wald test** assesses whether three emerging-specific market-state slope adjustments are all zero. Failure to reject this null does **not** prove that the two groups behave identically.

It also runs a Fama–French five-factor regression to examine momentum returns after controlling for standard equity risk factors:

$$
R_{WML,t}=\alpha+\beta_M(Mkt-RF)_t+\beta_S SMB_t+\beta_H HML_t+\beta_R RMW_t+\beta_C CMA_t+\varepsilon_t.
$$

## 6. Three Momentum Strategies

The project compares a static momentum exposure with two adaptive allocation rules.

### Strategy A: Static momentum

$$
R^{Static}_t=R_{WML,t}.
$$

This is a constant, unit exposure to the WML factor and is the benchmark for comparing risk management.

### Strategy B: Volatility-managed momentum

The idea is simple: **reduce exposure when recent momentum volatility is high**. Define lagged 12-month momentum variance and inverse-variance exposure as

$$
v_t=\frac{1}{\left(\sigma^{WML}_{t-1}\right)^2}.
$$

The Notebook normalizes $v_t$ using its *expanding* mean through the portfolio-formation date:

$$
c_t=\left(\frac{1}{N_t}\sum_{s\leq t}v_s\right)^{-1}.
$$

The final exposure is constrained between zero and three:

$$
w^{VM}_t=\min\left(3,\max(0,c_tv_t)\right),
$$

and the return is

$$
R^{VM}_t=w^{VM}_tR_{WML,t}.
$$

This rule can reduce or increase exposure to momentum, but it does not deliberately take the opposite side of the WML factor.

### Strategy C: Dynamic market-state momentum

The dynamic strategy first estimates the **expected WML return** using an expanding historical regression on the bear indicator, lagged market volatility, and their interaction:

$$
\widehat{\mu}_{WML,t}=\widehat{\alpha}_{t-1}+\widehat{\beta}_{1,t-1}Bear_{t-1}+\widehat{\beta}_{2,t-1}\sigma^{MKT}_{t-1}+\widehat{\beta}_{3,t-1}Bear_{t-1}\sigma^{MKT}_{t-1}.
$$

It then calculates a raw position:

$$
a_t=\frac{\widehat{\mu}_{WML,t}}{\left(\sigma^{WML}_{t-1}\right)^2}.
$$

Using an expanding mean of the absolute raw positions, the normalization is

$$
d_t=\left(\frac{1}{N_t}\sum_{s\leq t}|a_s|\right)^{-1}.
$$

The final position and return are

$$
w^{Dynamic}_t=\max\left(-3,\min(3,d_ta_t)\right),
$$

$$
R^{Dynamic}_t=w^{Dynamic}_tR_{WML,t}.
$$

Unlike volatility management, the dynamic rule may take **negative momentum exposure** when the estimated expected return is negative. It requires at least 60 valid historical regression observations after the state variables become available, resulting in an initial **84-month burn-in**.

Both adaptive strategies use lagged risk estimates and expanding normalization, rather than normalizing weights using the full future sample.

## 7. Selected Replication Results

The following values come from the **saved executed outputs** in the supplied Notebook. They describe historical, gross factor-strategy performance and should not be taken as live-trading forecasts.

### 7.1 Static momentum: performance and downside risk

| Metric | Developed | Emerging |
|---|---:|---:|
| Annualized mean return | 6.85% | 10.24% |
| Annualized volatility | 12.85% | 10.45% |
| Sharpe ratio | 0.53 | 0.98 |
| Maximum drawdown | -43.61% | -37.26% |
| Monthly 5% VaR | -5.01% | -3.67% |
| Monthly 5% CVaR | -9.32% | -7.24% |

Both market groups earned positive average WML returns while experiencing substantial historical drawdowns and negatively skewed returns. In April 2009, developed-market WML fell approximately **24.26%** in a single month.

### 7.2 Historical crash frequencies by previous market state

| Market | 5% crash rate after bear states | 5% crash rate after non-bear states |
|---|---:|---:|
| Developed | 16.67% | 2.98% |
| Emerging | 8.63% | 2.56% |

The descriptive crash frequency is higher following bear states in both groups. The regression evidence is more nuanced: estimated coefficients and their significance depend on the market, model, and crash definition. The pooled joint Wald test for the three emerging-specific slope adjustments has a p-value of approximately **0.491**.

### 7.3 Comparing strategies

| Market | Strategy | Annualized return | Annualized volatility | Sharpe | Max. drawdown |
|---|---|---:|---:|---:|---:|
| Developed | Static | 6.85% | 12.85% | 0.53 | -43.61% |
| Developed | Volatility-managed | 6.52% | 7.54% | 0.86 | -10.74% |
| Developed | Dynamic | 10.03% | 12.21% | 0.82 | -26.18% |
| Emerging | Static | 10.24% | 10.45% | 0.98 | -37.26% |
| Emerging | Volatility-managed | 15.65% | 12.14% | 1.29 | -15.45% |
| Emerging | Dynamic | 11.31% | 9.24% | 1.22 | -11.66% |

The adaptive rules achieve higher historical Sharpe ratios than their static counterparts in these outputs. However, the **strategies do not start on the same date**: volatility management requires 12 initial months, while dynamic timing requires 84. The table preserves the Notebook's strategy-specific samples. A common-sample comparison is preferable for a strictly like-for-like assessment.

These returns exclude trading costs, financing costs, leverage costs, and implementation frictions.

## 8. Risk Measures

The Notebook reports annualized mean returns, annualized volatility, Sharpe and Sortino ratios, skewness, excess kurtosis, historical Value at Risk (VaR), Conditional Value at Risk (CVaR), and maximum drawdown.

For monthly factor returns, annualization is calculated as

$$
\mu_{ann}=12\overline{R},\qquad \sigma_{ann}=\sqrt{12}\,\sigma_{monthly}.
$$

The reported Sharpe ratio is the annualized mean factor-strategy return divided by annualized volatility:

$$
Sharpe=\frac{\mu_{ann}}{\sigma_{ann}}.
$$

The monthly historical tail statistics are

$$
\mathrm{VaR}_{0.05} = Q_{0.05}(R_t)
$$

$$
\mathrm{CVaR}_{0.05} = \mathbb{E}[R_t \mid R_t \leq Q_{0.05}(R_t)]
$$

CVaR is the **average return among the worst 5% of observed months**, while maximum drawdown measures the largest peak-to-trough fall in cumulative wealth:

$$
MDD=\min_t\left(\frac{W_t}{\max_{s\leq t}W_s}-1\right).
$$

These measures complement the Sharpe ratio by describing the magnitude and persistence of downside losses.

## 9. Repository Files and Outputs

```text
AE_Momentum_Crashes_across_Developed_and_Emerging_Markets/
├── AE_published_paper.pdf
├── Developed_5_Factors.csv
├── Developed_MOM_Factor.csv
├── Emerging_5_Factors.csv
├── Emerging_MOM_Factor.csv
├── Momentum_Crashes_International_Revision_OneClick.ipynb
└── README.md
```

Executing the Notebook automatically creates `revision_outputs/tables/` and `revision_outputs/figures/`. The exported results include:

| Output | Content |
|---|---|
| Tables 1–2 | Full-sample/subperiod performance and worst momentum months |
| Table 3 | Conditional WML-return regressions and crash-probability regressions |
| Table 4 | Rebound mechanism and pooled cross-market comparison |
| Table 5 | Fama–French five-factor regressions |
| Table 6 | Static, volatility-managed, and dynamic strategy performance |
| Table 7 | Subperiod, volatility-window, and crash-threshold robustness checks |
| Additional table | Crash frequencies by preceding bear state |
| Figures 1–2 | Static momentum cumulative wealth and drawdowns |
| Figure 3 | Fitted crash probabilities across volatility states |
| Figures 4–5 | Developed- and emerging-market strategy comparisons |

Tables are exported as CSV files; figures are saved as PNG files.

## 10. One-Click Replication

The analysis is contained in **one Jupyter Notebook**, without project-specific packages, class definitions, or a complex directory structure.

### Step 1 — Get the project files

Clone the repository:

```bash
git clone https://github.com/XUAN-FENG9/Quant-Finance-and-Investment-Research.git
```

Open the `AE_Momentum_Crashes_across_Developed_and_Emerging_Markets` folder, or download that folder's files. Keep the four CSV datasets together in one directory.

### Step 2 — Install the dependencies

```bash
pip install numpy pandas scipy matplotlib statsmodels notebook
```

### Step 3 — Open the Notebook

```bash
jupyter notebook Momentum_Crashes_International_Revision_OneClick.ipynb
```

### Step 4 — Change one path

In the first code cell, replace the example value of `DATA_DIR` with the **absolute path to the folder containing all four CSV files**:

```python
DATA_DIR = r'D:\Research\Momentum_Crashes'
```

Do not rename the CSV files or place them in nested subfolders.

### Step 5 — Run all cells

Choose **Kernel → Restart & Run All**. The Notebook will clean and merge the input files, construct market states, estimate the regressions, build the trading strategies, create tables and charts, and save outputs under `revision_outputs/` inside your chosen data directory.

At the end, reproducibility assertions check the expected sample-length relationships, and the Notebook prints the output paths.

## 11. Interpretation and Limitations

The analysis provides a reproducible framework for studying international momentum crashes, but several qualifications are important:

- **Historical association is not causality.** A bear-state or volatility coefficient does not alone establish a causal crash mechanism.
- **Extreme events are rare.** Crash estimates may change with the sample period and tail threshold.
- **In-sample fit is not a forecasting guarantee.** Trading-rule performance also requires evaluation on genuinely unseen data.
- **Trading is not frictionless.** Actual implementation may involve transaction costs, market impact, shorting constraints, financing expenses, and leverage restrictions.
- **Different histories affect comparison.** The static and managed strategies use different effective samples unless explicitly aligned.
- **Regional factors are not individual-country portfolios.** Developed and emerging factor series summarize different underlying investment universes.

The most useful conclusion is therefore not that one timing rule will always outperform, but that **momentum's downside behavior depends on market conditions, and explicit risk management can materially change its historical risk profile**.

## 12. Further Reading and Citation

For the complete discussion and published research, see:

**[Published article in *Applied Economics* — DOI: 10.1080/00036846.2026.2749703](https://doi.org/10.1080/00036846.2026.2749703)**

Related foundational work includes:

- Jegadeesh, N., and Titman, S. (1993). “Returns to Buying Winners and Selling Losers: Implications for Stock Market Efficiency.” *The Journal of Finance*, 48(1), 65–91.
- Daniel, K., and Moskowitz, T. J. (2016). “Momentum Crashes.” *Journal of Financial Economics*, 122(2), 221–247.
- Barroso, P., and Santa-Clara, P. (2015). “Momentum Has Its Moments.” *Journal of Financial Economics*, 116(1), 111–120.
- Moreira, A., and Muir, T. (2017). “Volatility-Managed Portfolios.” *The Journal of Finance*, 72(4), 1611–1644.
- Fama, E. F., and French, K. R. (2017). “International Tests of a Five-Factor Asset Pricing Model.” *Journal of Financial Economics*, 123(3), 441–463.

Please cite the published article when using its research findings or adapting its empirical framework, and consult the paper for the formal bibliographic details.

---

*This repository is intended for academic research and educational use. Historical factor returns and simulated portfolios do not constitute investment advice or represent achievable live-trading performance.*
