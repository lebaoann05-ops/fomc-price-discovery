# FOMC Announcements and the Overnight–Intraday Return Relationship in the U.S. Stock Market: Evidence from SPY

**Group 12 · TCH442 Quantitative Methods for Finance**

**Research sample:** 2,764 SPY trading days, January 2015–December 2025

**Draft for group review, 29 September 2026**

## Abstract

This paper examines whether scheduled Federal Open Market Committee (FOMC) announcement days are associated with a different relationship between overnight and intraday returns in the U.S. equity market. We use daily adjusted open and close prices of SPY, an exchange traded fund tracking the S&P 500, and 87 scheduled FOMC announcement dates from 2015 to 2025. Our main ordinary least squares specification interacts overnight return with an announcement indicator and reports Newey–West standard errors. The estimated interaction is positive (0.192) but imprecise (p = 0.430). We therefore do not find convincing evidence that the overnight–intraday relationship changes on announcement days. In a separate model, absolute intraday return is about 13.42 basis points higher on scheduled FOMC days after controls (p = 0.049 with five Newey–West lags). This second result is sensitive to the bandwidth choice: its p-value is 0.053 with one lag and approximately 0.048–0.049 with five to twenty lags. Diagnostics find heteroskedasticity, serial correlation, volatility clustering and evidence of mean-model misspecification. The results describe conditional associations, not causal effects of monetary-policy surprises.

## 1. Research question and motivation

Monetary-policy announcements are among the most closely watched scheduled events in financial markets. A daily closing-price return, however, combines price movements that occur before a statement with movements during the trading session. This aggregation can conceal when information enters the market. We therefore separate SPY's daily log return into the prior-close-to-open overnight component and the open-to-close intraday component. Our primary question is whether scheduled FOMC announcement days change the conditional relationship between these two components. A positive overnight–intraday slope means that overnight movements tend to continue within the trading session; a negative slope suggests reversal. The interaction with the FOMC indicator measures whether that slope differs on scheduled announcement days.

The project also asks whether intraday movements are larger on such days and whether the pattern differs across rate hikes, cuts and holds. The second question concerns the magnitude of a return, regardless of direction. It is a useful complement: an announcement could increase trading-day movement without producing a stable positive or negative mean return. We investigate these questions using publicly available data and techniques covered in TCH442, particularly interaction terms, event indicators, time-series regression, diagnostic testing and heteroskedasticity-and-autocorrelation-consistent inference.

Our use of “relationship” in the title is deliberate. An FOMC announcement day is not a randomly assigned treatment. Policymakers respond to economic conditions, and investors form expectations before the announcement. The announcement indicator records a date, not the unexpected component of a policy decision. Accordingly, we do not attribute the estimated associations solely to monetary policy.

## 2. Context and related research

[Bernanke and Kuttner (2005)](https://doi.org/10.1111/j.1540-6261.2005.00760.x) study U.S. stock-price responses to monetary-policy changes and emphasize the role of the *unanticipated* component of a rate decision. Their design helps explain why our simple hike/cut/hold classification cannot identify a policy shock. It also motivates our caution about causal interpretation.

[Lucca and Moench (2015)](https://doi.org/10.1111/jofi.12196) document substantial U.S. equity returns before scheduled FOMC decisions in an earlier sample. Their result makes the period before the statement important in its own right. Our daily overnight measure is not the same as their pre-announcement window: it ends at the market open, while the statement is normally released later. We therefore examine a related timing question without claiming a direct replication.

[Kurov, Wolfe and Gilbert (2021)](https://doi.org/10.1016/j.frl.2020.101781) report that the historical pre-FOMC drift had largely disappeared after 2015 in their extended sample. This suggests that earlier average-return patterns should not be assumed to persist throughout our 2015–2025 period. It also gives a reason to inspect sample stability rather than rely only on a full-sample estimate.

Finally, [Andersen, Bollerslev, Diebold and Vega (2006)](https://www.federalreserve.gov/pubs/ifdp/2006/871/ifdp871.pdf) study real-time price discovery across stock, bond and foreign-exchange markets using high-frequency data. Their work illustrates why precisely timed surprises are preferable for identifying reactions to news. Our daily data cannot isolate the minutes after an FOMC statement; this is a central limitation of the present project.

## 3. Data and variable construction

We combine SPY daily adjusted open, high, low and close prices, volume and dividend information from [Yahoo Finance through `yfinance`](https://ranaroussi.github.io/yfinance/) with the daily VIX closing level from the same provider. The fixed analysis window is 1 January 2015 to 31 December 2025. Scheduled FOMC statement dates and decision classifications come from the [Federal Reserve's meeting calendar and historical materials](https://www.federalreserve.gov/monetarypolicy/fomccalendars.htm). The event file in the repository records each meeting date, decision type and source URL. Market-data downloads are cached locally; the notebook can reconstruct them from the declared source.

Let `Open_t` and `Close_t` denote adjusted SPY prices. We define overnight return as `ln(Open_t / Close_{t-1})` and intraday return as `ln(Close_t / Open_t)`. These log components add exactly to the close-to-close log return, which the notebook verifies numerically. Absolute intraday return is the absolute value of the intraday component. The announcement indicator equals one on a scheduled FOMC statement date. Separate hike, cut and hold indicators describe the direction of the target-rate decision, not whether it surprised investors. Controls include the previous day's SPY return, one-day-lagged standardized log VIX, weekday indicators and month indicators. The volatility model also controls for the absolute overnight return and the previous day's absolute close-to-close return.

The cleaned sample contains 2,766 trading days. The baseline excludes 3 March 2020, an unscheduled emergency decision on a trading day, and 16 March 2020, the first trading day after the emergency Sunday decision of 15 March. This leaves 2,764 observations: 87 scheduled FOMC days and 2,677 other days. The 87 decisions comprise 20 hikes, 9 cuts and 58 holds. The data audit checks duplicate dates, missing or infinite required values, positive prices and VIX, nonnegative volume, plausible open-high-low-close ordering, and the log-return identity. Large but genuine market moves are retained in the baseline.

On scheduled FOMC days, average overnight return is 16.92 basis points (bp), compared with 3.26 bp on other days. Average intraday return is −6.05 bp on FOMC days versus +2.10 bp otherwise. Mean absolute intraday return is 68.19 bp on FOMC days and 56.29 bp otherwise. These comparisons are descriptive: the two sets of days may differ in volatility conditions, calendar composition or other news.

## 4. Econometric design

The main regression is

`intraday_t = α + β overnight_t + γ FOMC_t + δ (overnight_t × FOMC_t) + θ′ controls_t + ε_t`.

Here `β` is the slope on ordinary days, `β + δ` is the slope on scheduled FOMC days, and `δ` is our main test of whether the relationship differs. `γ` is the conditional level shift when overnight return is zero. These are conditional regression coefficients, not effects of an exogenous policy shock.

The second regression uses absolute intraday return as its outcome and includes the FOMC indicator, absolute overnight return, lagged absolute close return, lagged VIX and calendar controls. Its announcement coefficient measures the difference in average intraday movement after those controls. The third specification replaces the single announcement indicator with hike, cut and hold indicators and their separate interactions with overnight return. We test their coefficients both individually and jointly, because reading only one small p-value from several related coefficients can be misleading.

All three models use ordinary least squares coefficients with Newey–West standard errors and a baseline bandwidth of five trading days. This inference method allows heteroskedasticity and short-run residual autocorrelation while leaving coefficient estimates unchanged. We report 95% confidence intervals and test the two main coefficients again with 1, 10 and 20 HAC lags. We also estimate separate 2015–2019 and 2020–2025 regressions, include emergency dates as a check, exclude ex-dividend days, and rerun the main model without observations flagged by Cook's distance. The Cook threshold `4/n` is used as a screening convention, not as a rule that crisis observations are invalid.

## 5. Main results and economic interpretation

The estimated ordinary-day overnight–intraday slope is 0.0047 (p = 0.918), close to zero. The FOMC interaction is +0.1919 with a HAC standard error of 0.2431, p = 0.430 and a 95% confidence interval from −0.2845 to +0.6683. Its point estimate suggests a more positive slope on scheduled FOMC days, but the interval includes both a meaningful negative and a meaningful positive change. We cannot conclude that the slope truly differs. A nonsignificant test does not demonstrate that the difference equals zero; it indicates that the available observations do not pin it down precisely. The estimated FOMC-day level shift at zero overnight return is −12.85 bp (p = 0.260), also statistically inconclusive.

The absolute-return model gives a more suggestive finding. The FOMC coefficient is 0.001342 in log-return units, or +13.42 bp of absolute intraday return. Its HAC standard error is 6.82 bp; the five-lag p-value is 0.049. The 95% interval is approximately +0.05 to +26.79 bp. Relative to the ordinary-day mean absolute intraday return of 56.29 bp, the coefficient is economically noticeable, but the lower confidence bound is nearly zero. It would be incorrect to describe the estimate as a 13.42% increase in the stock price: it is a 0.1342 percentage-point difference in *absolute intraday log return*. It measures movement magnitude and does not predict whether SPY rises or falls.

The separate decision-type model has limited power, especially for the nine cut dates. The hike interaction is about +1.154 (individual p = 0.077), while the cut and hold interactions are less precise or close to zero. Joint HAC Wald tests provide the appropriate group-level view. The three slope shifts jointly equal zero with p = 0.328; the three level shifts jointly equal zero with p = 0.566. Equality of slope shifts across the decision groups is not rejected (p = 0.214), nor is equality of their level shifts (p = 0.501). Thus the data do not establish systematic differences among hikes, cuts and holds. These labels also omit what investors expected before each meeting.

A descriptive `[-2,+2]` trading-day window shows average event-day overnight return of +16.92 bp and intraday return of −6.05 bp. The event-day average close-to-close return is about +10.86 bp. These are SPY returns rather than abnormal returns relative to a market benchmark; other same-day information may contribute. They should be read as a visualization of timing and scale, not as a causal event-study estimate.

## 6. Diagnostics and sensitivity

Formal tests reinforce the need for careful inference. For both the main return model and the absolute-return model, Breusch–Pagan and White tests reject constant residual variance (both p < 0.001). Breusch–Godfrey tests at five lags reject no residual serial correlation (both p < 0.001). ARCH-LM tests reject no conditional variance dependence (both p < 0.001). These findings support using HAC standard errors and make volatility clustering substantively relevant. An augmented Dickey–Fuller test does not reject a unit root in the SPY price level (p ≈ 0.996), while it rejects a unit root in the overnight, intraday and close-to-close return series (each p < 0.001). This supports working with returns instead of regressing trending price levels on event indicators. A stationarity test on returns does not imply that volatility is constant.

Ramsey RESET with heteroskedasticity-robust covariance rejects the tested linear mean specifications. RESET cannot tell us which omitted variable or nonlinear term is responsible, but it warns against treating the simple conditional mean models as a complete description. An exploratory quadratic overnight-return extension did not improve fit in our check, so we retain the stated baseline rather than select terms to obtain a preferred p-value. HAC changes estimated uncertainty under serial dependence; it does not remove omitted-variable bias or fix a wrong functional form.

The main interaction conclusion is highly stable to the HAC bandwidth: its p-values are 0.432, 0.430, 0.429 and 0.432 for 1, 5, 10 and 20 lags, respectively. In contrast, the absolute-return FOMC coefficient keeps the same positive point estimate but has p-values 0.053, 0.049, 0.048 and 0.048. Its classification at the conventional 5% threshold therefore depends slightly on a reasonable modeling choice. We call it a borderline or suggestive association, not a robust discovery at exactly 5%.

Excluding SPY ex-dividend dates leaves the main interaction positive (+0.173, p = 0.477). Including the two emergency-impact dates gives +0.191 (p = 0.433). Cook's-distance screening flags 167 baseline observations; excluding them produces +0.133 (p = 0.155). This trimmed regression is a sensitivity check, not the preferred model, because market turmoil is real financial information. The interaction changes sign from −0.136 in 2015–2019 (p = 0.831) to +0.284 in 2020–2025 (p = 0.318), but neither period estimate is precise. Different signs do not by themselves prove a structural break.

As a supplementary volatility exercise, the notebook estimates a univariate GARCH(1,1) model on percentage-scaled intraday log returns and plots fitted conditional volatility. Its estimated shock coefficient is `α = 0.170`, its lagged-variance coefficient is `β = 0.792`, and persistence `α + β = 0.963`. This high sum indicates that volatility shocks dissipate slowly in the fitted model. Since FOMC is not included in that variance equation, the fit characterizes volatility clustering in SPY and cannot be interpreted as an announcement effect. The direct announcement comparison remains the absolute-return regression above.

## Results tables

**Table 1. Descriptive SPY returns, 2015–2025.** Returns are shown in basis points (bp).

| Day type | Trading days | Mean overnight (bp) | Mean intraday (bp) | Mean absolute intraday (bp) |
|---|---:|---:|---:|---:|
| Other trading days | 2,677 | +3.26 | +2.10 | 56.29 |
| Scheduled FOMC days | 87 | +16.92 | −6.05 | 68.19 |

**Table 2. Main HAC regression estimates.** Model 1 outcome is intraday log return; Model 2 outcome is absolute intraday log return. Five HAC lags are used. Coefficients and standard errors marked “bp” have been multiplied by 10,000.

| Model and term | Coefficient | HAC SE | 95% confidence interval | p-value |
|---|---:|---:|---:|---:|
| Model 1: overnight return | +0.0047 | 0.0459 | [−0.0853, +0.0948] | 0.918 |
| Model 1: FOMC day, bp | −12.85 | 11.41 | [−35.23, +9.52] | 0.260 |
| Model 1: overnight × FOMC | +0.1919 | 0.2431 | [−0.2845, +0.6683] | 0.430 |
| Model 2: FOMC day, bp | +13.42 | 6.82 | [+0.05, +26.79] | 0.049 |

**Table 3. Joint HAC Wald tests for decision types.** These tests refer to Model 3.

| Restriction | Wald χ² | Degrees of freedom | p-value |
|---|---:|---:|---:|
| All hike/cut/hold slope shifts = 0 | 3.446 | 3 | 0.328 |
| All hike/cut/hold level shifts = 0 | 2.031 | 3 | 0.566 |
| Level shifts equal across decisions | 1.381 | 2 | 0.501 |
| Slope shifts equal across decisions | 3.087 | 2 | 0.214 |

**Table 4. Diagnostic-test p-values.** All tests use the baseline 2,764-observation sample. BP and White test constant error variance; BG tests serial correlation at five lags; ARCH-LM tests conditional variance dependence at five lags; RESET tests the fitted mean form using HC3 covariance.

| Diagnostic | Model 1 p-value | Model 2 p-value |
|---|---:|---:|
| Breusch–Pagan | <0.001 | <0.001 |
| White | <0.001 | <0.001 |
| Breusch–Godfrey (5 lags) | <0.001 | <0.001 |
| ARCH-LM (5 lags) | <0.001 | <0.001 |
| Ramsey RESET (HC3) | <0.001 | 0.002 |

**Table 5. HAC bandwidth sensitivity.** Coefficients remain unchanged when only the covariance bandwidth changes.

| HAC maximum lags | Interaction coefficient | Interaction p-value | FOMC absolute-return effect (bp) | Volatility p-value |
|---:|---:|---:|---:|---:|
| 1 | +0.1919 | 0.432 | +13.42 | 0.053 |
| 5 | +0.1919 | 0.430 | +13.42 | 0.049 |
| 10 | +0.1919 | 0.429 | +13.42 | 0.048 |
| 20 | +0.1919 | 0.432 | +13.42 | 0.048 |

**Table 6. Main interaction across sample checks.** p-values use five HAC lags.

| Sample check | Observations | Interaction estimate | p-value |
|---|---:|---:|---:|
| Baseline | 2,764 | +0.192 | 0.430 |
| Emergency-impact dates included | 2,766 | +0.191 | 0.433 |
| Ex-dividend dates excluded | 2,720 | +0.173 | 0.477 |
| Cook-flagged observations excluded | 2,597 | +0.133 | 0.155 |
| 2015–2019 | 1,258 | −0.136 | 0.831 |
| 2020–2025 | 1,506 | +0.284 | 0.318 |

## 7. Limitations and conclusion

Daily open and close prices cannot isolate the immediate response after the FOMC statement or the later press conference. Overnight return stops at the morning opening bell and is therefore an imperfect measure of pre-announcement anticipation. The analysis identifies scheduled dates but does not observe the unexpected component of the target-rate decision, statement, projections or communication. VIX is a broad uncertainty proxy and other economic announcements can arrive on the same day. The sample contains only 87 scheduled events, with particularly few cuts. RESET rejection indicates that the selected mean equations may omit relevant nonlinearities or variables. The approximately 5% volatility p-value is sensitive to the HAC bandwidth. These limitations constrain both precision and causal interpretation.

Our main finding is that the 2015–2025 sample does not provide convincing evidence that scheduled FOMC announcements change the overnight–intraday return relationship in SPY. The positive interaction is too imprecisely estimated to determine even its direction with confidence. Scheduled announcement days are associated with larger absolute intraday returns by about 13.42 bp after controls, but that evidence is borderline and should be described accordingly. This distinction between an inconclusive slope result and a suggestive movement-magnitude result is the project's central empirical contribution. The project illustrates how to interpret economic magnitude, uncertainty, diagnostics and sample sensitivity together instead of treating a single p-value as the whole result.

## References and data sources

- Andersen, T. G., T. Bollerslev, F. X. Diebold and C. Vega (2006), “Real-Time Price Discovery in Global Stock, Bond and Foreign Exchange Markets,” Federal Reserve International Finance Discussion Papers 871. https://www.federalreserve.gov/pubs/ifdp/2006/871/ifdp871.pdf
- Bernanke, B. S. and K. N. Kuttner (2005), “What Explains the Stock Market's Reaction to Federal Reserve Policy?”, *Journal of Finance* 60, 1221–1257. https://doi.org/10.1111/j.1540-6261.2005.00760.x
- Kurov, A., M. H. Wolfe and T. Gilbert (2021), “The Disappearing Pre-FOMC Announcement Drift,” *Finance Research Letters* 40, 101781. https://doi.org/10.1016/j.frl.2020.101781
- Lucca, D. O. and E. Moench (2015), “The Pre-FOMC Announcement Drift,” *Journal of Finance* 70, 329–371. https://doi.org/10.1111/jofi.12196
- Federal Reserve, FOMC meeting calendars and historical statement links, 2015–2025. https://www.federalreserve.gov/monetarypolicy/fomccalendars.htm
- Yahoo Finance, SPY daily adjusted OHLC, dividends and volume; `^VIX` daily close, 2014–2025 download window via `yfinance`. https://finance.yahoo.com/

## Submission note

This is a group-editable report draft. Add the four students' names, verify all 87 event-date source links, and attach one signed contribution paragraph per member before submitting the course copy. The accompanying notebook and generated CSV tables reproduce the reported calculations.
