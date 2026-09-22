# Infrastructure ETFs: Factor Exposures, Regime Risk, and Portfolio Roles

This folder contains the replication materials for the empirical analysis in
"Infrastructure ETFs: Factor Exposures, Regime Risk, and Portfolio Roles."

## CRSP Data Availability

The analysis uses monthly fund data from the CRSP Survivor-Bias-Free U.S.
Mutual Fund Database accessed through WRDS.

CRSP data are proprietary and subject to institutional licensing restrictions.
Accordingly, raw or derived CRSP observations are not redistributed in this
public repository.

The repository provides the CRSP fund-number list used in the study so that
researchers with valid WRDS/CRSP access can reconstruct the required input data.

## Reconstructing the CRSP Input

Licensed WRDS users should access the CRSP Survivor-Bias-Free U.S. Mutual Fund
Database and retrieve the following monthly fields for the `crsp_fundno`
identifiers provided in this repository:

- `MONTHLY_RETURNS`: `crsp_fundno`, `caldt`, `mret`
- `MONTHLY_NAV`: `crsp_fundno`, `caldt`, `mnav`
- `MONTHLY_TNA`: `crsp_fundno`, `caldt`, `mtna`

Merge the three monthly datasets using `crsp_fundno` and `caldt`.

Retrieve the available monthly history for the listed funds through
April 30, 2026. The strategy-start dates, investability screens, minimum-history
requirements, and other sample-construction rules are implemented in the
replication notebook and described in the manuscript.

For local replication, save the merged CRSP input as:

`filtered_fund_return.csv`

This file should be stored locally in the input location specified in the
notebook and should NOT be committed to the public repository.

## Reproduction

1. Obtain the licensed CRSP data following the instructions above.
2. Reconstruct `filtered_fund_return.csv`.
3. Set the local CRSP data path in the notebook.
4. Run the notebook from beginning to end.

The notebook reproduces the reported sample construction, fund-level
statistics, archetype portfolios, factor regressions, regime analysis,
robustness checks, and portfolio applications.

Other non-CRSP inputs are either included with the replication materials or
obtained from the public sources documented in the notebook.

## Data License

Users are responsible for complying with their institution's WRDS/CRSP license
terms. No CRSP return, NAV, or total-net-asset observations are distributed in
this repository.
