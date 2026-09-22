# Infrastructure ETFs: Factor Exposures, Regime Risk, and Portfolio Roles

This folder contains the replication materials for the empirical analysis in
**“Infrastructure ETFs: Factor Exposures, Regime Risk, and Portfolio Roles.”**

## Repository Contents

The main replication files are:

- `JAI_Infrastructure_ETFs_FINAL_Reproducible_Analysis.ipynb`  
  Main notebook reproducing the empirical analysis reported in the manuscript.

- `JAI_infrastructure_ETF_fund_codes_space_separated.txt`  
  Space-separated list of the CRSP fund identifiers (`crsp_fundno`) used in the study.  
  This file is provided so that licensed WRDS/CRSP users can upload or paste the fund-code list directly into the WRDS query interface when extracting the required CRSP Mutual Fund data.

- `F-F_Research_Data_5_Factors_monthly.csv`  
  Monthly Fama-French five-factor data.

- `F-F_Momentum_Factor_monthly.csv`  
  Monthly momentum-factor data.

- `THREEFY10.csv`  
  U.S. 10-year Treasury yield data.

- `VIXCLS.csv`  
  VIX data used in the regime analysis.

## CRSP Data Availability

The study uses monthly fund data from the **CRSP Survivor-Bias-Free U.S. Mutual Fund Database**, accessed through WRDS.

CRSP data are proprietary and subject to institutional licensing restrictions. Accordingly, raw or derived CRSP observations are **not redistributed** in this public repository.

Instead, the repository provides the CRSP fund-number list used in the study:

`JAI_infrastructure_ETF_fund_codes_space_separated.txt`

The file contains the relevant `crsp_fundno` identifiers in space-separated format and is intended to facilitate direct use in WRDS by researchers with valid CRSP access.

## Reconstructing the CRSP Input from WRDS

Licensed WRDS users should access the **CRSP Survivor-Bias-Free U.S. Mutual Fund Database** and use the fund identifiers contained in:

`JAI_infrastructure_ETF_fund_codes_space_separated.txt`

to restrict the query to the funds used in the study.

Retrieve the following monthly variables:

- `MONTHLY_RETURNS`: `crsp_fundno`, `caldt`, `mret`
- `MONTHLY_NAV`: `crsp_fundno`, `caldt`, `mnav`
- `MONTHLY_TNA`: `crsp_fundno`, `caldt`, `mtna`

Merge the three monthly datasets using:

`crsp_fundno` and `caldt`

Retrieve the available monthly history for the listed funds through **April 30, 2026**.

The final local CRSP input should therefore contain at least the following columns:

`caldt, crsp_fundno, mtna, mret, mnav`

For convenience, the reconstructed file may be saved locally as:

`filtered_fund_return.csv`

The replication notebook automatically identifies the CRSP input from these required column names, so the local filename may differ.

**Do not upload or commit the reconstructed CRSP data file to a public repository.**

## Running the Replication

1. Obtain the CRSP data from WRDS using the supplied `crsp_fundno` list.
2. Merge the monthly return, NAV, and TNA data as described above.
3. Place the reconstructed CRSP file together with the other required input files in a local data folder.
4. Change only the `DATA_DIR` line near the beginning of the notebook to point to that folder.
5. Run the notebook from beginning to end.

The notebook reproduces the manuscript's sample construction, fund-level statistics, archetype portfolios, factor regressions, correlation and clustering analysis, regime analysis, robustness checks, and portfolio applications.

Strategy-start dates, investability screens, minimum-history requirements, archetype classifications, and other sample-construction rules are implemented in the notebook and described in the manuscript.

## Data Licensing

Users are responsible for complying with the terms of their institutional WRDS/CRSP license.

No CRSP monthly return (`mret`), NAV (`mnav`), or total-net-asset (`mtna`) observations are distributed in this repository.
