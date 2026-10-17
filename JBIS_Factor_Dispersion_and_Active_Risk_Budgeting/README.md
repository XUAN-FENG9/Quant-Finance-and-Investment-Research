
# Factor Dispersion and Active-Risk Budgeting: Evidence from Factor Momentum

**Research and replication materials**  
**Authors:** Xuan Feng, Erwang Li, and Zhuohang Li  
**Paper:** [Read the preprint](preprint_JBIS_factor_dispersion.pdf)  
**Notebook:** [Open the complete analysis](JBIS_Factor_Dispersion_JOI_OneClick.ipynb)

## 1. What is the paper about?

A factor manager faces three separate decisions:

1. **Opportunity:** How different might factor returns be next month?
2. **Direction:** Which factors should receive more or less capital?
3. **Risk:** How far should the portfolio deviate from its benchmark?

We introduce a **Factor Dispersion Index (FDI)** to inform the first and third questions. FDI measures how far the monthly returns of seven factors are spread apart *after adjusting for each factor's typical volatility*. We then test whether it predicts future differences in factor performance and whether it improves factor-momentum allocations.

**The main finding:** High FDI tends to precede *larger differences in outcomes*, but not reliably *higher average momentum profits*. FDI is better interpreted as an additional active-risk indicator than as a signal to turn a strategy on or off.

## 2. Data and sample

The study uses seven monthly U.S. research factor return series:

| Factor | Meaning | Data source |
|---|---|---|
| `MKT` | Market excess return | Kenneth R. French Data Library |
| `SMB` | Small minus big (size) | Kenneth R. French Data Library |
| `HML` | High minus low book-to-market (value) | Kenneth R. French Data Library |
| `RMW` | Robust minus weak profitability | Kenneth R. French Data Library |
| `CMA` | Conservative minus aggressive investment | Kenneth R. French Data Library |
| `MOM` | Stock-level momentum factor | Kenneth R. French Data Library |
| `BAB` | Betting against beta | AQR Data Library |

Monthly factor data run from **July 1963 to March 2026 (753 months)**. Daily VIX observations from FRED are averaged by month for additional checks. After the volatility-estimation period and the minimum FDI-state history, the main portfolio comparison contains **657 monthly returns, July 1971–March 2026**.

**Important distinction:** `MOM` is one of the seven input factors. **Factor Momentum** in this project is a *second-level strategy* that ranks and reallocates **across all seven factors**; it is not the same thing as the underlying `MOM` factor.

## 3. How the Factor Dispersion Index works

Let $r_{i,t}$ be factor $i$'s return in month $t$, and let $\sigma_{i,t-1}^{(36)}$ be its monthly return standard deviation estimated over the **36 months ending at $t-1$**. Define the standardized return:

$$
z_{i,t}=\frac{r_{i,t}}{\sigma_{i,t-1}^{(36)}}.
$$

For $N=7$ factors, let $\bar z_t=N^{-1}\sum_{i=1}^N z_{i,t}$. FDI is the cross-sectional sample standard deviation:

$$
FDI_t=\sqrt{\frac{1}{N-1}\sum_{i=1}^{N}(z_{i,t}-\bar z_t)^2}.
$$

**Example:** If all factors have similar standardized returns, FDI is low, even if they all rise. If some factors rise sharply while others fall, FDI is high. FDI does **not** identify the winners, predict a positive average return, or measure the volatility of a single portfolio.

To make the indicator usable without hindsight, each month's FDI is ranked against the history **available up to that month**. After at least 60 valid FDI observations, the expanding percentile $P_t$ assigns the state:

$$
S_t=\begin{cases}
\text{Low}, & P_t<1/3,\\
\text{Middle}, & 1/3\le P_t<2/3,\\
\text{High}, & P_t\ge 2/3.
\end{cases}
$$

The current month's information is used to classify its state; **only the following month's returns** evaluate investment decisions.

## 4. Factor selection is not the same as portfolio allocation

### A. Momentum-selection diagnostic: positive **and negative** factor weights

At month-end $t$, each factor receives a score based on its *compounded return over the most recent 12 months*, divided by its *current 36-month annualized volatility*:

$$
Score_{i,t}=\frac{\prod_{k=0}^{11}(1+r_{i,t-k})-1}{\sqrt{12}\,\sigma_{i,t}^{(36)}}.
$$

Rank the seven scores from 1 (lowest) to 7 (highest), then form a centered ranking signal:

$$
q_{i,t}=\frac{Rank_{i,t}-4}{3}\in[-1,1].
$$

The **selection diagnostic** uses all seven ranks, with positive exposure to recent winners and negative exposure to recent losers:

$$
a_{i,t}=2\frac{q_{i,t}/\sigma_{i,t}^{(36)}}{\sum_{j=1}^{N}\left|q_{j,t}/\sigma_{j,t}^{(36)}\right|},\qquad
\sum_i|a_{i,t}|=2.
$$

Its next-month **momentum selection spread** is simply the return on these signed factor positions:

$$
Spread^{MOM}_{t+1}=\sum_i a_{i,t}r_{i,t+1}.
$$

The reversal diagnostic takes the opposite positions: $Spread^{REV}_{t+1}=-Spread^{MOM}_{t+1}$. These are **diagnostic returns**, not returns on the later benchmark-relative investment portfolios. In particular, the selection diagnostic is **not just long the top two and short the bottom two**, and absolute weights summing to 2 do not necessarily imply zero net exposure.

### B. Momentum Tilt: nonnegative factor-allocation weights

For a practical allocation test, we first construct a **Risk-Balanced** benchmark using inverse-volatility weights:

$$
b_{i,t}=\frac{1/\sigma_{i,t}^{(36)}}{\sum_j1/\sigma_{j,t}^{(36)}}.
$$

The **Momentum Tilt** strategy increases the benchmark allocations of recent winners and reduces those of losers:

$$
w^{MOM}_{i,t}=\frac{b_{i,t}(1+0.75q_{i,t})}{\sum_jb_{j,t}(1+0.75q_{j,t})}.
$$

All these allocation weights are nonnegative because $1+0.75q_{i,t}\ge0.25$. The **Reversal Tilt** replaces the plus sign with a minus sign. Here, “nonnegative” refers to allocations **to the factor portfolios**; the underlying research factors may themselves contain long and short positions.

The **FDI-Aware Momentum** strategy uses the same rankings but scales the tilt by FDI state:

$$
w^{FDI\text{-}MOM}_{i,t}=\frac{b_{i,t}(1+0.75g_tq_{i,t})}{\sum_jb_{j,t}(1+0.75g_tq_{j,t})},\qquad
g_t=\begin{cases}0&\text{Low},\\0.5&\text{Middle},\\1&\text{High}.\end{cases}
$$

Thus it applies **no momentum tilt in Low FDI**, half the normal tilt in Middle FDI, and the full tilt in High FDI. **FDI-Aware Reversal** follows the same schedule but tilts toward past losers.

## 5. Tracking-error budgets and final portfolio scaling

**Tracking error (TE)** measures the risk of a portfolio's returns *relative to its benchmark*. If $\Delta w_t=w_t^{MOM}-b_t$ is the proposed momentum allocation minus benchmark allocation and $\widehat\Sigma_t$ is the annualized covariance matrix estimated from the latest 36 months, the predicted active risk is:

$$
\widehat{TE}_t=\sqrt{\Delta w_t^\top\widehat\Sigma_t\Delta w_t}.
$$

Two otherwise matched rules seek a **4% annual predicted TE** and limit the momentum-tilt multiplier to **1.25**:

$$
\begin{aligned}
k_t^{Constant}&=\min\left(1.25,\frac{0.04}{\widehat{TE}_t}\right),\\
k_t^{FDI}&=\min\left(1.25,\frac{0.04}{m_t\widehat{TE}_t}\right),\qquad
m_t=\begin{cases}0.80&\text{Low},\\1.00&\text{Middle},\\1.25&\text{High}.\end{cases}
\end{aligned}
$$

Each rule forms a candidate allocation $w_t=b_t+k_t\Delta w_t$. In the FDI-adjusted rule, **High FDI increases estimated active risk by 25%, so the same proposed tilt is scaled down**. This is different from the FDI-Aware Momentum “gate” above, which allows *more* tilt in High FDI.

Finally, **every strategy**, including the benchmark, is separately scaled toward **10% annual predicted total portfolio volatility**, with a maximum leverage multiplier of **2**:

$$
L_{p,t}=\min\left(2,\frac{0.10}{\sqrt{w_{p,t}^{\top}\widehat\Sigma_t w_{p,t}}}\right),\qquad
\widetilde w_{p,t}=L_{p,t}w_{p,t}.
$$

Here $w_{p,t}$ is the **complete candidate factor-weight vector** of strategy $p$; $L_{p,t}$ multiplies that whole vector, not just the momentum component. Because of caps and this final scaling, realized TE need not equal exactly 4%.

## 6. What does the evidence show?

- **Factor outcomes diverge more after High FDI.** The next-month standardized factor-return range averages **3.359** after High FDI versus **2.206** after Low FDI—about **52% wider**. This describes differences between factor outcomes, **not** a 52% investment return.
- **FDI predicts magnitude more clearly than direction.** High-minus-low average momentum-selection returns are **not statistically distinguishable**; by contrast, the absolute value of the next-month selection spread tends to rise with the FDI percentile.
- **FDI adds some information beyond historical tracking error.** On the same 657 months, a regression of next-month **absolute active return** on 12-month historical TE has an in-sample $R^2$ of **15.05%**; adding FDI percentile raises it to **17.64%**. This is *incremental in-sample explanatory power*, not proven out-of-sample forecast improvement.
- **Raw dispersion is a useful alternative, not an inferior indicator in every setting.** For next-month *raw* factor-return range, raw dispersion explains more variation than FDI; for *standardized* range, FDI is more informative.

### Portfolio results (net, annualized)

| Strategy | Return | Volatility | Sharpe |
|---|---:|---:|---:|
| Risk-Balanced | 8.72% | 7.45% | 1.17 |
| Momentum Tilt | 11.47% | 8.74% | 1.31 |
| FDI-Aware Momentum | 10.04% | 8.23% | 1.22 |
| Constant TE Budget | 12.06% | 9.26% | 1.30 |
| FDI-Informed TE Budget | 12.06% | 9.15% | 1.32 |

The FDI-Aware Momentum gate **underperforms** Momentum Tilt because it gives up momentum exposure in Low-FDI months. The two TE-budget strategies earn virtually **identical returns**: the extra return relative to Momentum Tilt primarily comes from **TE targeting itself**, not the FDI adjustment. The Sharpe advantage of Momentum Tilt over Risk-Balanced is not statistically established in the reported bootstrap comparison.

**BAB matters:** Rebuilding the strategy universe without BAB reduces the Momentum Tilt Sharpe from **1.31** to **1.10**, versus **1.07** for the six-factor Risk-Balanced benchmark. Conclusions therefore depend in part on the factor set and the investability of its constituents.

## 7. Reproduce the analysis

All required inputs are in **this folder**. Download or clone the repository, open the notebook, and change **only the `DATA_DIR` line** to the folder containing the four CSV files:

```python
from pathlib import Path
DATA_DIR = Path(r"C:\path\to\JBIS_Factor_Dispersion_and_Active_Risk_Budgeting")
```

Then choose **Run All** in Jupyter Notebook or JupyterLab. The analysis uses Python with `pandas`, `numpy`, `scipy`, `matplotlib`, and Jupyter/IPython. To install the basic dependencies:

```bash
pip install numpy pandas scipy matplotlib jupyter ipython
```

The notebook reads these exact source files (all in one directory, without nested input folders):

```text
F-F_Research_Data_5_Factors_monthly.csv
F-F_Momentum_Factor_monthly.csv
BAB data.csv
VIXCLS.csv
```

It creates a local **`fdi_notebook_outputs/`** folder containing CSV exhibit tables, FDI and momentum scores, monthly strategy weights, gross and net return series, turnover, regression and robustness outputs, figures, and JSON result summaries. Input data are not downloaded automatically during execution.

## 8. Important interpretation and implementation limits

The factor series are **research portfolios**, generally reported before trading costs within the factors, financing, management fees, and capacity constraints. The notebook applies an illustrative **10-basis-point charge per unit of one-way change in factor allocation weights**; it is **not** a complete live-implementation cost model. Portfolio returns are hypothetical, and past performance is not a forecast.

All predictive regressions are observational and primarily **in-sample**; they do not establish causality or guarantee improved live forecasting. FDI is conditional on the **seven factors supplied** and should be reconstructed using an investor's actual investable factor portfolios. Decisions about allocation size must also account for funding, liquidity, leverage restrictions, transaction costs, and investment-mandate limits.

**Practical takeaway:** Use **FDI to assess how widely factor outcomes may differ**, a **separate return signal** to choose allocations, and a **risk model and portfolio constraints** to determine position sizes. Do not treat high FDI as an automatic instruction to increase risk.

---

**Data sources:** [Kenneth R. French Data Library](https://mba.tuck.dartmouth.edu/pages/faculty/ken.french/data_library.html) · [AQR Data Library](https://www.aqr.com/Insights/Datasets) · [FRED VIXCLS](https://fred.stlouisfed.org/series/VIXCLS)
