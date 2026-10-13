# Pre-Registration: Gold Returns Decomposition Project

**Status:** Pre-registration complete and committed on Oct 13 2026.

<h2>Why</h2>
Gold is remarkable- it's rare, dense, chemically unreactive, extremely conductive (third highest, electrically and thermally, behind silver and copper) and unlike most metals, colored-- a  reddish-yellow which emerges from relativistic effects. These are just some of the properties that make it valuable: for jewelry, currency, and in technological applications. Gold is a literal symbol of wealth and financial stability, and people have returned to it in times of economic uncertainty for thousands of years. Unlike other financial instruments, people of all cultures and socioeconomic backgrounds seem to appreciate its value, and for this reason I am interested in learning more about the spot price and what drives the changes of that price. After accounting for suspected dependencies of gold returns, is there structure in the residuals? If not, that is interesting, since that would mean I have found a model that explains gold returns well over the past 20 years. On the other hand, if there is structure that is interesting for a different reason: that structure may hold clues of its cause (major world events, changing economic regimes, change of gold's place in the economy, etc).

Previous work: Baur & Lucey (2010) found that gold acts as a short term safe-haven asset in times of financial turmoil. Baur (2011) investigated the effects of various known and suspected influences on gold returns at monthly frequency, and crucially found that regression on single variables can be extremely misleading. This highlighted the need for multiple regression in a strongly interacting system. Le & Chang (2012) investigated the effect of oil price shocks on gold returns and found that, while there is no significant long term linear relation between the two, in the short term gold returns respond strongly to oil price shocks. Baur & Smales (2020) found that geopolitical risk plays a different role than other kinds of risk in asset prices. Beckman and Czudaj (2019) found that the relation of gold with other assets varies in time.

<h2>Data and universe</h2>

**Main data**
Initially I wanted to use the actual spot price as the dependent variable, but I could not find a source for daily data that was not paywalled. Therefore, I use GLD (Yahoo Finance `GLD`) as a proxy for the spot price of gold. I will run the same analysis on IAU (Yahoo Finance `IAU`) to confirm any findings on GLD are not a fund artifact.
To check how closely these funds track gold spot price, I check their monthly averages against the monthly average spot price, derived from the World Bank Commodity Markets Pink sheet data (tabulated here https://datahub.io/core/gold-prices). I find that the monthly average of GLD is correlated with monthly spot price at 0.99748, and the monthly average of IAU is correlated with monthly spot price at 0.99755.
My universe nominally begins when the current trade-weighted dollar index begins, Jan 01 2006, and ends Jan 01 2026. Leaving out 2026 will allow me to check my results against what has happened from 2026 onward (note that this pre-registration is being published in October 2026, so the data exists for most of 2026, but I have not yet examined it. I only have general awareness of gold and the various regressors in the news as of October 2026). The practical range is Jan 04 2006-Dec 31 2025 because not all indices were available on Jan 1-3 2006 or Jan 1 2026, and because I take differences which removes the first universally available value. 

**Regressors**
1. 10 Year TIPS (FRED `DFII10`). The market yield on US Treasury Securities at 10 year constant maturity, inflation indexed. Tracks real interest rates (and therefore cost of carry/opportunity cost of holding gold as an asset). I use two degrees of freedom (this and the breakeven rate, below) to separate the two components of real interest rates defined by the [Fisher relation](https://en.wikipedia.org/wiki/Fisher_equation).
2. 10 Year breakeven (FRED`T10YIE`). The breakeven inflation rate derived from 10 year securities, representing the expected/nominal inflation rates. Separately, since inflation is roughly the rate of change of prices, it tracks general economic uncertainty.
3. The US dollar (FRED `DTWEXBGS`). This which represents the dollar by a broad trade-weighted index (basically, a large geometric mean over exchange rates with the largest US trading partners). Known to be strongly tied to gold-- both historical reasons (gold standard) and because gold spot price that I'm considering is in USD (should help separate out exchange rate shifts in gold spot price)
4. Market volatility (FRED `VIXCLS`), which measures the market expectation of near-term volatility measured by stock index option prices.
5. S&P 500 (Yahoo Finance `^GSPC`). General market health indicator. Based on gold's known role as a safe-haven asset, I suspect this will be anti-correlated with gold spot 
6. Geopolitical risk (`GPRD_ACT` from [Caldara & Iacoviello](https://www.matteoiacoviello.com/gpr.htm)), specifically risk based on geopolitical acts (war declarations, terror attacks, etc). This is intended to track uncertainty based on actual events in international relationship stability.
7. Geopolitical risk (`GPRD_THREAT` from [Caldara & Iacoviello](https://www.matteoiacoviello.com/gpr.htm)), specifically risk based on geopolitical threats (posturing, military buildups, etc). This is intended to track uncertainty based on unrealized events in international relationship stability.
8. Oil (FRED`DCOILWTICO`) Le & Chang (2012) found that in the short term gold returns respond strongly to oil price shocks.
9. Copper (Yahoo Finance `HG=F`). Since copper is an industrial metal, this should track with the industrial demand component of gold, leaving only the currency/investment/commodity part to be accounted for.

**Considered regressors**. These were explicitly excluded from this study. I included all the regressors From Baur (2011)'s trimmed list, except the following:
	1. S&P GSCI as of 2025 this is composed of 7% gold. Baur used this as a regressor but the 7% gold weighting should produce inflated regression coefficients. Instead, I include an index that tracks copper, which would represent the industrial component of gold without the explicit direct dependance.
	2. Dow Jones. Baur also regressed on this. I drop this in favor of the broader S&P 500 since the Dow is only ~30 companies which tend to be strongly correlated with each other.
	3. US CPI. This tracks prices of consumer goods, and would have been nice to include, but is only available at monthly frequency.
	4. US TBill 2nd market 6 month. Intentionally not included as I already use 10Y TIPS as general indicator of interest rates.
	5. US Treasury constant 30 year. Intentionally not included as I already use 10Y TIPS as general indicator of interest rates.

**Transform rules** Log difference vs simple difference:
- GLD - price-like - difference of log 
- IAU - price-like - difference of log
- Copper - price-like - difference of log
- S&P500 - price-like -  difference of log
- Dollar - price-like -  difference of log
- Oil - price-like. But theres a day where it went negative (4/20/2020), which would cause log difference to contaminate that day and those around it. Don't want to throw out this day since that was an interesting and turbulent time. Oil also stays centered around a consistent mean for the entire period of my universe -  difference
- GPRD_ACT, GPRD_THREAT - normalized to have average 100. this can take the value zero but does not do so in my universe. - difference
- VIX - rate-like, also looks centered around constant mean - difference
- DFII10/TIPS - interest rate, in percent; difference
- T10YIE/breakeven - interest rate, in percent; difference
  
**Other rules:**
- Data combined with an inner join, then I drop any resulting NaNs
- All prices pulled from market data will be the close price for that day.
- A one day lag could be informative to separate timing effects (gold prices and various indices being posted at different times in the day) from actual dependence. I decided to exclude lags because it is likely a lagged regressor would be highly correlated with its current time counterpart, which could pose a problem with my choice of OLS as an estimator.
- The primary analysis will be on the OLS coefficients as described above. I will run the analysis again on standardized data and report the resulting coefficients so the coefficients can be compared directly.


<h2>The question</h2>
After accounting for interest and inflation (10 year TIPS, breakeven), market fear/health (VIX, S&P500), the dollar, geopolitical uncertainty, copper and oil does the residual in daily gold returns exhibit structure-- volatility clustering or regime dependence-- (also explore autocorrelation) beyond white noise? 

<h2>Analysis</h2>

<h3>Notes about hierarchies below</h3>
	These are intended to protect the project from multiple testing. 
  - Initial analysis: I don't make any claims about the results of the regression directly-- this only produces processed information for further analysis. 
  - Primary: means I present a hypothesis that is rejected or not based on standard significance threshold ($\alpha=0.05$). 
  - Secondary: means I reduce the threshold by the number of tests in the secondary family; eg with five tests, the standard significance threshold would be reduced by five to $1\%$, $\alpha=0.05/5$. Any results from secondary analyses are supporting and carry reduced importance relative to the primary. 
  -Exploratory: means I am not making any claim, but am still interested in the results, which may provide a clue to a future investigation. 
  -Check: means I am making no claim, but the results of these tests serve as sanity checks on the validity of other parts of my analysis.

<h3>Analysis plan</h3>

**Initial analysis** OLS with heteroskedasticity and autocorrelation consistent (HAC, [Newey-West](https://en.wikipedia.org/wiki/Newey%E2%80%93West_estimator)) covariance on gold returns (for my sample size ~5000, I set the bandwidth $9=\text{ceil}(5000^{1/4})$), yielding coefficient vectors for each of the timespans below: 

- The entire universe: Jan 4 2006- Dec 31 2025
- Four regimes, separated by shifts in monetary policy
  1. Jan 1 2006- Dec 16, 2008. Ends when the Fed cuts rates to zero lower bound. 
  2. Dec 17 2008-Feb 19 2020 is post-Lehman and pre-COVID. Ends at pre-COVID equity peak.
  3. Feb 20 2020- Mar 15 2022. Active pandemic era; emergency monetary policies. Bounded on the right by the Fed raising rates above zero lower bound.
  4. Mar 16 2022-Dec 31 2025. Post pandemic era and interest rate hikes.
       
**Primary test** I'm primarily interested in coefficient stability over different financial regimes. 
- Estimator: Wald test with HAC (bandwidth = 9, as above). This is a joint test on all four of the periods above.
- Estimand: coefficient vector from the OLS regression
- Null hypothesis: all four coefficient vectors are equal simultaneously. In other words, my model coefficients are stable across all four regimes.
- Alternative hypothesis: coefficient vector varies depending on the regime.
- Significance threshold: if the Wald test returns a p-value less than $\alpha = 0.05$, I will reject the null hypothesis. Note that multiplicity is handled through the degrees of freedom here-- 10 coefficients mean $(4-1)\times10 = 30$ degrees of freedom and the test statistic is distributed as $\chi^2_{30}$.
     
**Secondary test** I am wondering if my model accounts for all volatility clustering-- that is, after subtracting the influence of the regressors, how much detectable volatility clustering is intrinsic to gold returns themselves. This also serves as a check on the additional exploration below: if there is no autocorrelation, then any results from GARCH are based on noise and don't support actual volatility clustering.
- Estimator: [ARCH-LM](https://www.mathworks.com/help/econ/engles-arch-test.html) at $q=5$ (to check short-term clustering) and $q=20$ (set by the [typical](https://vlab.stern.nyu.edu/docs/volatility/GARCH) upper-end timescale of equity markets)
- Estimand: residuals from the full universe
- Null hypothesis: there is no autocorrelation (ARCH-LM coefficients at all lags are zero) in squared residuals at lags 5 and 20
- Alternative hypothesis: there is autocorrelation in the squared residuals at lags 5 and 20
- Significance threshold: Since I will have conducted two tests ($q=5$ and $q=20$), reduce the significance threshold to $\alpha/2=0.025$ 
    
**Exploratory** 

**Additional exploration based on primary test** I strongly suspect that gold returns, like most financial data, are heteroskedastic. I'm interested in how much, if any, heteroskedasticity my residuals inherit from the raw return data.
  - Run GARCH(1,1) on raw full-universe gold returns themselves, as well as on the full-universe residuals
  - Report the sum of GARCH model coefficients $(\alpha+\beta)$ for both raw returns and for residuals, along with the standard error from the GARCH model.
  - Calculate the difference between $(\alpha+\beta)$ for the two GARCH models as a quantitative estimate of how much volatility clustering my residuals inherited from raw returns
  - **Check** Connect the residuals from the separate subperiods into a single series that describes the entire 20 year span. Fit GARCH(1,1) to this composite residual and compare the resulting $(\alpha+\beta)$ to the one found from the residuals above.  If they are similar, that supports the interpretation of volatility being real volatility, rather than apparent volatility due to parameter drift over the different subperiods. 

**Investigate autocorrelations in residuals** By designating this investigation as exploratory, I mean that I will make no claim about the results and simply report them (to avoid multiple-testing-based dilution of my main result). Perhaps the results will inform a future study.
  - Estimator: Ljung-Box test at lag 20
  - Estimand: residuals from the full universe

<h2>Deviations log</h2>
