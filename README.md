# gold-returns-decomposition
A project analyzing the residuals after regressing gold returns on known dependent variables, with a daily timescale.

**Status:** In progress.

## Approach

Gold returns (GLD) are modeled against real rates (10Y TIPS), inflation
expectations (10Y breakeven), the trade-weighted dollar, equity
volatility (VIX), industrial metals (COMEX copper), oil (WTI), equity
market returns (S&P 500), and geopolitical risk (Caldara–Iacoviello
GPR, threat and act components).

The residual is then characterized for autocorrelation, volatility
clustering, and regime dependence.

## Pre-registration

The specification, hypotheses, and analysis protocol are committed in
[PREREGISTRATION.md](PREREGISTRATION.md) prior to examination of
residuals.

## Data

All data is pulled from freely available sources (Yahoo Finance, FRED,
[https://www.matteoiacoviello.com/gpr.htm|Caldara–Iacoviello GPR]). Raw data is not committed; see `src/` for the
retrieval pipeline.
