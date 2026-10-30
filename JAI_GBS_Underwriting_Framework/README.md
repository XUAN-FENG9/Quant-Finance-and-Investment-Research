
# GPU-Based Securities: An Underwriting Framework for AI Compute Securitization

**Journal:** The Journal of Alternative Investments (JAI)

This repository provides the data and Python code for reproducing the quantitative analysis in *GPU-Based Securities: An Underwriting Framework for AI Compute Securitization*.

## 1. Research Overview

As demand for artificial intelligence grows, GPU infrastructure is increasingly financed through secured lending and structured credit.

This study examines how GPU assets and contracted computing revenues can support asset-backed securities with senior, mezzanine, and equity tranches.

The main questions are:

- How much debt can GPU infrastructure safely support?
- How do rental prices, operating costs, and technological depreciation affect investor losses?
- Can GPU redeployment and refinancing improve recovery after default?

The analysis combines publicly available market data with a transparent cash-flow model and Monte Carlo simulations.

## 2. Key Findings

- **Credit risk:** Baseline expected principal losses are approximately 0.09% for senior notes and 9.2% for mezzanine notes.
- **Stress sensitivity:** Under moderate commercial stress, expected losses increase to 0.43% and 21.3%, respectively.
- **Maturity and recovery:** Approximately 98.6% of baseline paths require a mezzanine maturity extension. Continuing GPU operations can improve recovery relative to immediate liquidation, although refinancing is not guaranteed.

These results are model-based scenarios rather than observed historical security returns.

## 3. Data and Repository Contents

The dataset is compiled from publicly available information through **September 11, 2026**.

| File | Description |
|---|---|
| `00_research_design_data_map.csv` | Research design and data structure |
| `01_gpu_specs.csv` | NVIDIA H100, H200, and B200 specifications |
| `02_gpu_cloud_rental_prices.csv` | GPU rental quotations from five cloud providers |
| `03_gpu_secondary_market.csv` | Secondary-market GPU valuation evidence |
| `04_gpu_backed_financing_transactions.csv` | GPU-backed financing transactions |
| `05_coreweave_operating_panel.csv` | CoreWeave quarterly operating data |
| `06_adjacent_datacenter_abs.csv` | Comparable data-center ABS transactions |
| `07_source_registry.csv` | Original data sources and references |
| `08_data_quality_and_design_adjustments.csv` | Data quality and methodological adjustments |
| `GBS_JAI_OneClick_Analysis.ipynb` | Complete reproducible Python analysis |

Public rental quotations include CoreWeave, AWS, Lambda, RunPod, and Nebius. Published rental prices are used as market references rather than assumed guaranteed contractual payments.

## 4. Model Framework

### Capital Structure

The illustrative transaction uses a normalized equipment acquisition and deployment cost of 100, with a reserve of 2.

$$
100 + 2 = 60 + 15 + 27
$$

where:

- Senior debt = 60
- Mezzanine debt = 15
- Sponsor equity = 27, including the funded reserve

Senior principal amortizes over 48 months. Mezzanine principal is due at month 48, with a possible resolution extension to month 60.

### GPU Rental Pricing

The reference GPU mix is 50% H100, 30% H200, and 20% B200.

$$
p_{\mathrm{ref}} =
0.50p_{\mathrm{H100}}
+ 0.30p_{\mathrm{H200}}
+ 0.20p_{\mathrm{B200}}
$$

The resulting reference rental price is approximately **USD 5.45 per GPU-hour**.

Actual rental revenue is adjusted using a realization factor:

$$
p_{\mathrm{realized}} = \alpha p_{\mathrm{ref}}
$$

where $\alpha$ represents the proportion of the public reference price achieved under the modeled commercial arrangements.

The baseline assumes $\alpha = 0.75$. Lower realization rates are tested to assess financing risk.

### Expected Principal Loss

For each debt tranche, expected principal loss is calculated as:

$$
EL = \frac{1}{N}\sum_{i=1}^{N}\frac{L_i}{P_0}
$$

where:

- $N$ = number of simulated paths
- $L_i$ = unpaid principal in simulation $i$
- $P_0$ = initial tranche principal

The model also reports loss probabilities, cash-flow IRRs, maturity extensions, and recovery outcomes.

## 5. How to Reproduce the Results

### Step 1: Install Python Packages

Use Python 3 with Jupyter Notebook and install the required libraries:

```bash
pip install numpy pandas matplotlib scipy pillow jupyter
```

### Step 2: Download the Files

Download the notebook and all nine CSV files into **one local folder**, without additional subfolders.

Open:

`GBS_JAI_OneClick_Analysis.ipynb`

### Step 3: Set the Data Directory

Near the beginning of the notebook, locate:

```python
DATA_DIR = Path(r".")
```

If all files are in the same folder as the notebook, no change is required.

Otherwise, replace the path with your local data folder, for example:

```python
DATA_DIR = Path(r"C:\Research\GBS")
```

Then select:

**Kernel → Restart & Run All**

Only the `DATA_DIR` line needs to be changed.

## 6. Outputs and Robustness Tests

The notebook automatically creates a folder named:

`GBS_JAI_outputs`

It generates:

- Publication-quality exhibits and figures
- GPU pricing and collateral summaries
- Monthly cash-flow and debt-service calculations
- Baseline, moderate, downside, and severe scenario results
- Senior and mezzanine loss and return estimates
- Liquidation, continued-operation, and conditional-refinancing comparisons
- Rental-price, operating-cost, and other sensitivity analyses
- CSV results and parameter documentation

The principal model uses **12,000 Monte Carlo paths per scenario**, with fixed random seeds for reproducibility. Additional sensitivity experiments use separately specified simulation counts.

## 7. Data Availability and Replication

All input data were manually collected and compiled from public sources. The repository contains the processed datasets, Python analysis code, Monte Carlo simulations, robustness tests, and supporting replication materials.

The quantitative results can be reproduced locally using the provided files and notebook, without proprietary databases or external API access.

The model is designed as an investor-oriented underwriting framework, not as a credit rating model or a historical default estimator.
