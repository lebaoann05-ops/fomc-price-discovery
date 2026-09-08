# FOMC Announcements and Price Discovery

> **Group 12 — Quantitative Methods for Finance (TCH442)**  
> *Overnight anticipation and intraday return dynamics in the US stock market*

## Project overview

This project examines whether scheduled Federal Open Market Committee (FOMC) announcements change the relationship between overnight and intraday returns of the SPDR S&P 500 ETF Trust (SPY).

Instead of studying only SPY's total daily return, we decompose each trading day into two economically distinct periods:

- **Overnight return:** information incorporated between the previous close and the current open.
- **Intraday return:** price movement between the current open and close.

This decomposition allows us to investigate whether price movements observed before the market opens tend to continue or reverse during the trading session—and whether that pattern changes when the Federal Reserve announces a monetary-policy decision.

## Research question

> Do scheduled FOMC announcement days alter the continuation or reversal relationship between SPY's overnight and intraday returns?

The analysis is associational rather than strictly causal. FOMC decisions respond to economic conditions that may also affect financial markets.

## Motivation

FOMC statements are normally released while the US stock market is open. Consequently, a daily close-to-close return combines at least two different information periods: market expectations formed before the opening bell and reactions occurring during the trading session.

Separating these components provides a more informative view of price discovery than a conventional event-day dummy applied only to total daily returns. The results may help explain when monetary-policy information is incorporated into market prices and whether pre-market movements are reinforced or corrected during FOMC sessions.

## Hypotheses

- **H1 — Event-day effect:** Average intraday returns differ between scheduled FOMC days and ordinary trading days.
- **H2 — Price-discovery effect:** The relationship between overnight and intraday returns changes on scheduled FOMC days.
- **H3 — Volatility effect:** Absolute intraday returns are higher on scheduled FOMC days.
- **H4 — Decision heterogeneity:** Market responses differ across rate hikes, rate cuts, and decisions that leave the target rate unchanged.

## Data

| Dataset | Variables | Intended period | Source |
|---|---|---:|---|
| SPY daily prices | Open, Close, adjusted prices, volume | 2015–2025 | Yahoo Finance via [`yfinance`](https://ranaroussi.github.io/yfinance/) |
| Market uncertainty | VIX closing level | 2015–2025 | Yahoo Finance via `yfinance` |
| FOMC calendar | Scheduled meeting and statement dates | 2015–2025 | [Federal Reserve](https://www.federalreserve.gov/monetarypolicy/fomccalendars.htm) |
| Policy decisions | Hike, cut, or hold classification | 2015–2025 | Federal Reserve statements |

The main analysis uses only scheduled announcement dates. Unscheduled or emergency decisions—particularly those in 2020—will be excluded from the baseline and considered separately in a robustness check.

## Variable construction

For trading day \(t\):

$$
r^{ON}_t = \ln\left(\frac{Open_t}{Close_{t-1}}\right)
$$

$$
r^{ID}_t = \ln\left(\frac{Close_t}{Open_t}\right)
$$

where \(r^{ON}_t\) is the overnight return and \(r^{ID}_t\) is the intraday return.

Additional variables include:

- `fomc_day`: 1 on a scheduled FOMC statement date, 0 otherwise.
- `hike`, `cut`, `hold`: mutually exclusive policy-decision indicators.
- `abs_intraday_return`: absolute value of the intraday return.
- `lagged_return`: previous trading day's SPY return.
- `lagged_vix`: previous trading day's VIX level.
- Day-of-week and month indicators.

Adjusted prices will be used where appropriate. SPY ex-dividend dates will also be excluded in a robustness check because distributions can mechanically affect measured overnight returns.

## Econometric design

### Model 1 — Baseline price-discovery model

$$
r^{ID}_t = \alpha + \beta r^{ON}_t + \gamma FOMC_t
+ \delta\left(r^{ON}_t \times FOMC_t\right)
+ \theta'Controls_t + \varepsilon_t
$$

Key interpretation:

- \(\beta\): overnight–intraday continuation or reversal on non-FOMC days.
- \(\gamma\): difference in expected intraday return on FOMC days.
- \(\delta\): change in the overnight–intraday relationship on FOMC days.

### Model 2 — Intraday volatility

$$
|r^{ID}_t| = \alpha + \gamma FOMC_t + \theta'Controls_t + \varepsilon_t
$$

### Model 3 — Type of policy decision

The general FOMC indicator will be replaced by `hike`, `cut`, and `hold` indicators and their interactions with the overnight return.

All main regressions will report heteroskedasticity- and autocorrelation-consistent **Newey–West/HAC standard errors**. Statistical significance will be discussed alongside coefficient magnitude and economic significance.

## Event-study component

As a supporting analysis, the project will plot average returns within a `[-2, +2]` trading-day window around scheduled announcements:

- Average overnight return.
- Average intraday return.
- Average absolute intraday return.
- Cumulative close-to-close return.
- Comparisons among hike, cut, and hold decisions where sample sizes permit.

The event study is descriptive and complementary to the regression analysis; it is not treated as a separate research project.

## Current empirical results

The executed notebook currently finds:

- The `overnight return × FOMC day` interaction is `+0.192` with a HAC p-value of `0.430`; the main price-discovery hypothesis is therefore not supported at conventional significance levels.
- Scheduled FOMC days are associated with approximately `13.42` basis points higher absolute intraday return, with `p = 0.049`—statistically significant at 5%, though close to the threshold.
- The interaction changes sign between the 2015–2019 and 2020–2025 subsamples, indicating possible instability.
- Emergency FOMC market-impact dates are excluded from the baseline rather than incorrectly classified as ordinary trading days.
- Removing observations flagged by Cook's distance preserves the positive interaction sign, but the estimate remains statistically insignificant (`p = 0.155`).
- Results should be treated as conditional associations rather than causal effects or measures of monetary-policy surprise.

See the fully executed [analysis notebook](output/jupyter-notebook/fomc_data_and_models.ipynb) for data validation, coefficient tables, interpretations, and robustness checks.

## Robustness checks

The project uses four focused sensitivity checks:

1. Compare the clean baseline with a specification that includes the two emergency-policy market-impact dates in 2020.
2. Exclude SPY ex-dividend dates.
3. Exclude observations flagged by the conventional Cook's-distance screening rule, without treating the trimmed model as the preferred estimate.
4. Compare the `2015–2019` and `2020–2025` subsamples.

## Repository structure

```text
fomc-price-discovery/
├── README.md
├── requirements.txt
├── outputs/
│   └── jupyter-notebook/
│       ├── fomc_data_and_models.ipynb
│       ├── data/raw/fomc_scheduled_events_2015_2025.csv
│       └── outputs/
│           ├── figures/      # Event-window and influence diagnostics
│           └── tables/       # Regression and event-study tables
```

Generated files and downloaded data will be clearly separated from source code. Scripts will use relative paths and a fixed sample end date so the reported results can be reproduced later.

## Roadmap

- [x] Define the research question and project scope.
- [x] Collect and validate SPY and VIX data.
- [x] Build the FOMC event calendar; final manual audit remains.
- [x] Produce descriptive statistics and initial visualizations.
- [x] Estimate the baseline regression.
- [ ] Complete the first progress checkpoint.
- [x] Estimate the extended models and event-study results.
- [x] Run the pre-specified robustness checks.
- [ ] Write the report and contribution statement.
- [ ] Re-run the project from a clean environment before submission.

## Reproducibility

The final repository will include:

- A pinned `requirements.txt` file.
- A documented Python/Jupyter environment.
- Code that downloads or reconstructs the raw data where licensing permits.
- A fixed analysis period of 2015–2025.
- Automatically generated regression tables and figures.
- A clear record of any manually classified FOMC decisions.

## Current status

**Stage:** Data preparation and preliminary econometric analysis completed.  
**Course:** Quantitative Methods for Finance (TCH442).  
**Team:** Group 12 — four member roles to be added after task allocation.

## Limitations anticipated

- The study cannot fully isolate causal monetary-policy shocks without a separate measure of market surprise.
- Daily data cannot distinguish the immediate 2:00 p.m. statement reaction from price movements earlier in the session.
- Hike/cut/hold classifications do not measure whether a decision was expected.
- Results for policy-decision subgroups may be imprecise because the number of FOMC events is limited.

These constraints are part of the intended project scope and will be stated explicitly rather than addressed with unavailable intraday or proprietary futures data.
