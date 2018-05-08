# Risk management 

"*Risk management is a flawed concept*" - Nassim Nicholas Taleb

*Risk* = effect of uncertainty on objectives; the risk is the possibility that an event will occur and adversely affect the achievement of an objective.

## Risk Management vs Risk Engineering

Risk is basically uncertainty with implicit consequences. In general, the investor cares about two types of risk: 1) those for which she receives compensation in return for bearing, and 2) those for which no compensation is received.  It is obvious that the latter must be eliminated to the extent possible.

For example, the following shows risk disected into systematic risk and unsystematic risk:


![Related image](https://www.tytoncapital.com/wp-content/uploads/2016/05/types-of-risk.gif)

We can observe that unsystematic risk can be *completely* eliminated from the portfolio by sufficiently diverisfying or simply having a sufficient number of assets in the portfolio. Of course, these assets must be reasonably uncorrelated with each other.

Critically, an investor is NOT compensated for bearing  unsystematic risk, and this would be detrimental to the portfolio's return (and the investor's wealth) over time.


## Ex Post -> "Management"
Risks which have materialized should be managed to the extent possible. Examples:
- A position in the portfolio experiences positive returns to the point that the allocation breaches a concentration guideline. Managing this concentration risk means liquidating a portion of the position to return the position to a tolerable percentage of the portolio. The proceeds are then allocated to other existing or new positions at the discretion of the investment manager.
- A position's price volatility increases to the point that it becomes too "risky" for the portfolio. This could be regarding the allocation or in general. In the former case, the position would be reduced so that the asset-weighted volatility of the portfolio returns to tolerable levels

## Ex Ante -> "Engineering"
There are potential risks that we can explicity prohibit with blockchain tooling. This is where we want to shine. 

Portfolio guidelines are rules which are laid out in a legal document (Offering Memorandum or Prospectus) prior to fund inception. Here, the parties agree on the types of instuments to be invested, strategy, rules about concentration and other rule-based metrics.


All institutional fund managers use software to help them manage portfolios.  They will say, "Our software will not allow a trade which conflicts with the guidelines", or they will have a process in place which has a name like "pre-trade clearance" or "hypothetical trading". These methods have served the industry to a satisfactory level, but non-compliant trades do find their way into a portfolio.


**Different risks in traditional RM**:
- Market risk - It is probably what the RM spends most time but usually involves Monitor /Report/Enforce risk limits, position size, VAR, Stressing Portfolio, Scenario Analysis or otherwise quantifying the P&L of movements in the underlying positions.

- Counterparty Risk - analyzing default of risk of trade counterparties

- Enterprise risk - large umbrella that represents 'business risk' and could be things like computers going down, risk of redemptions, model validation and many other

- Back office/Marketing/IR/Performance

### What is risk management ? 
- the **forecasting and evaluation of financial risks** together with the **identification of procedures to avoid or minimize their impact**
- **identification, evaluation and prioritization of risks** followed by coordinated and economical application of resources to minimize, monitor and control the probability or impact of unfortunate events.
- Risk management's objective is to **assure uncertainty does not deflect the endeavor from the business goals**.
- Strategies to manage threats, typically includes: **avoiding the threat, reducing the negative effect or probability of the threat, transferring all or part of the threat to another party** and even retaining some or all of thepotential or actual consequences of a particular threat. 
- Risk with greatest loss or impact and the greatest probability of occurring are handled first, and risks with lower probability of occurrence and lower loss are handled in descending order.


In Melon, it's different. We're not doing risk management, rather are we doing *risk engineering* ie. anticipating the risk ahead of time, and handling it in the code of the smart contract. Being proactive vs reactive. 

*risk engineering* = the anticipation and identification of different risks ahead of time, and the action of preventing their occurence through code. Rules coded into smart contracts that are mean. Making sure that something **will not** happen in the context of a Melon fund.

**What risks can we identify within the context of a Melon fund?**

- Malevolent behavior of fund manager (trying to embezzle funds through orders on exchanges)
- Bad trading decisions from fund manager
- Deviation from initial strategy as laid out in the prospectus from fund manager
- Fund manager doesn't adhere to the level of risk that was communicated to the investors (volatility risk, sharpe ratio)
- Redemption risk
- Liquidity risk

**What could on-chain risk engineering entail?**

- Prevent investors from fund embezzlement through exchanges.
- Ensure that the fund operates accordingly to approved fund characteristics and other risk guidelines.
- Trading limits: Approve or reject trades based on:
        - is asset authorized?
        - is the asset liquid enough?
        - is the post trade holdings allocation acceptable? -Nominal (gross/net positions, concentration)
        - is the price *reasonable* (ie. best execution)?
        - is the volatility implications of this trade acceptable according to portfolio guidelines?
- Pricefeed providing additional data on assets, useful to risk management such as historical volatility, average annual rates of return and average standard deviation. Is that conceivable?

**Which kind of off-chain risk engineering tooling can we provide?**
- Risk engineering should also emcompass monitoring of portfolio's risk in light of market risk. Simple monitoring can be done off-chain at the discretion of the fund manager. Monitoring of indicators such as downside capture [1], drawdown [2], leverage etc. 
- Possible to link up your fund with risk engineering trading bot such as rebalancing bots (eg. rebalancing based on allocation or rebalancing based on risk parity). Could be simple trading bots, not necessarily enforced on-chain.
- Tools to monitor VaR: measures the dollar-loss expectation that can occur with a 5% probability.
- Tools to monitor redemption risk (based on redemption requests, redemption history and in light of current portfolio allocation) 

[1]    ([**Downside Capture**](https://www.investopedia.com/university/hedge-fund/risks.asp))
In relation to hedge funds, and in particular those that claim absolute return objectives, the measure of [downside capture](https://www.investopedia.com/terms/d/down-market-capture-ratio.asp) can indicate how correlated a fund is to a market when the market declines. The lower the downside capture, the better the fund preserves wealth during market downturns. This metric is figured by calculating the cumulative return of the fund for each month that the market/benchmark was down, and dividing it by the cumulative return of the market/benchmark in the same time frame. Perfect correlation with the market will equate to a 100% downside capture and typically is only possible when comparing the benchmark to itself.

[2]   ([**Drawdown**](https://www.investopedia.com/university/hedge-fund/risks.asp))
Another measure of a fund's risk is maximum [drawdown](https://www.investopedia.com/terms/d/drawdown.asp). Maximum drawdown measures the percentage drop in cumulative return from a previously reached high. This metric is good for identifying funds that preserve wealth by minimizing drawdowns throughout up/down cycles, and gives an analyst a good indication of the possible losses that this fund can experience at any given point in time. Months to recover, on the other hand, gives a good indication of how quickly a fund can recuperate losses. Take the case where a hedge fund has a maximum drawdown of 4%, for example. If it took three months to reach that maximum drawdown, as investors, we would want to know if the returns could be recovered in three months or less. In some cases where the drawdown was sharp, it should take longer to recover. The key is to understand the speed and depth of a drawdown with the time it takes to recover these losses. Do they make sense given the strategy?

## Rule set identification and specification

### Ex ante risk engineering

#### Rule 1: Maximum number of positions

**Definition**: A fund may not exceed X positions. 
**Rationale**: (i) Avoid over-diversification. (ii) Avoid excessive rebalancing transactions in the event of a new subscription (or redemption).
**Implementation**: Before a trade is authorized, the system must get the number of different assets held by the fund, and make sure that adding 1 asset does not breach the X maximum number of positions.
**Parameters**: X as the maximum number of underlying positions.

#### Rule 2: Whitelisted assets 

**Definition**: The fund can only invest in explicitely permitted assets (included in the whitelisted asset universe defined at fund setup).
**Rationale**: (i) Investors have a clear understanding of which assets can ever be invested in by the fund. (ii) Restrict the investment manager. Tighten scope of the investment universe. (iii) Fund can only invest in a niche classification (eg. ESG, utility tokens, virutal currencies, commodities only etc.)
**Implementation**: Before a trade is authorized, the system must check that the asset being bought is included in the whitelisted asset universe of the fund. The asset registrar may contain an additional field _categories_ that informs about the nature/type of the asset. This _category_ field can then be queried by the contract before authorizing trading of the asset. 
Other relevant categories: type (utility, security etc.), sector, country (of jurisdiction) etc.
**Parameters**: Whitelisted assets list or list of category (with specification of _AND_ or _OR_ relationship)
**Side note**: There could be a method to remove an asset from the whitelist. Fund manager should not be able to add an asset to the whitelist.

#### Rule 3: Blacklisted assets 

**Definition**: The fund is explicitely prohibited from investing in some assets.
**Rationale**: (i) Investors have a guarantee that the fund will never invest in a type of asset. (ii) Restricts Investment manager (iii) Regulatory compliance (iv) Investor preferences.
**Implementation**: Before a trade is authorized, the system must check that the asset being bought is not included in the blacklisted assets list. The asset registrar may contain an additional field _categories_ that informs about the nature/type of the asset. This _category_ field can then be queried by the contract before authorizing trading of the asset. 
**Parameters**: Blacklisted assets list
**Side note**: There could be a method to add an asset from the backlist. Fund manager should not be able to remove an asset from the blacklist.


#### Rule 4: Max concentration (%)

**Definition**: A position (or positions in a single category) can not actively exceed X% of the NAV.
**Rationale**: (i) Avoid cluster risk. (ii) Portofolio diversification (iii) Reduce unsystematic risk. 
**Implementation**: Before a trade is authorized, the system must check the current proportion of NAV that this asset (or category) represents and computes the post trade proportion of NAV. If the post trade proportion of NAV exceeds X%, the trade will not be authorized. 
**Parameters**: X as the maximum % a position's value can represent of the NAV.

#### Rule 5: Early redemption penalty (this may be contained in the compliance contract)

**Definition**: An investor can be incurred a penalty if he redeems his shares before the end of the specified period.
**Rationale**: VC funds, private equity funds. Longer time horizon funds. 
**Implementation**: This is not part of the risk management contract per se, rather should be implemented as a compliance/participation module. 
**Parameters**: period (probably expressed in # of blocks before redemption is possible) and penalty.


#### Rule 6: Best price execution

**Definition**: A fund manager is not allowed to deviate more than X% away from the reference price provided by the pricefeed.
**Rationale**: (i) Prohibit predatory investment manager behavior. (ii) Washtrading mitigation
**Implementation**: Before a trade is authorized, the system must check the price of the order against the reference price and ensure the former does not deviate away more than X% from the reference price.
**Parameters**: x as % of allowed deviation. 

#### Rule 7: Turnover (expressed in # of trades)
**Definition**: Restrict the number of trades. The fund manager is restricted from exceeding the maximum number of trades allowed. 
**Rationale**: (i) Prevent predatory behavior from the investment manager. (ii) Washtrading prevention. (iii) Keep transactions fees to the minimum.
**Implementation**: Before a trade is authorized, the system must check if this additional trade does not exceed the maximum # of trades allowed on the defined time period.
**Parameters**: max number of trades, time period.

Consider volume turnover in terms of portfolio base currency.

Note, top-ups (reductions) due to subscriptions (redemptions) should not count as turnover. Only discretionary investment decisions

#### Rule 8: Turnover (expressed in volume traded)
**Definition**: Restrict the number of trades. The fund manager is restricted from exceeding the maximum volume in trades allowed in a given timeframe. 
**Rationale**: (i) Prevent predatory behavior from the investment manager. (ii) Washtrading prevention. (iii) Keep transactions fees to the minimum.
**Implementation**: Before a trade is authorized, the system must check if this additional trade does not exceed the maximum volume of trades allowed on the defined time period.
**Parameters**: max number volume of trades (expressed in the currency of denomination of the fund), time period.

Consider volume turnover in terms of portfolio base currency.

Note, top-ups (reductions) due to subscriptions (redemptions) should not count as turnover. Only discretionary investment decisions

#### Rule 9: Market cap range
**Definition**: A fund can only invest in assets whose market cap is contained in a certain range [x;y] (ie x < market cap < y). 
**Rationale**: Strategy focus enforce
**Implementation**: Before trade is authorized, the system must check the market cap of the assets being traded and verify that the market cap is compliant with the market cap range defined at fund setup. The asset registrar may contain an additional field _market cap_ for each asset. Open question: how to measure Market Cap. Only influences the trade permission at time of trade. That is, there is no consequence when an owned asset's market cap breaches the upper or lower specified bounds.
**Parameters**: x as low boundary of the range and y as high boundary of the range.

#### Rule 10: Volatility threshold for a specific asset
**Definition**: A fund can only invest (purchase) in an asset whose volatility reference indicator is contained in a certain range [x;y] (ie x < volatility < y). 
**Rationale**: Investors have an understanding of the volatility level of individual assets that are allowed in the portfolio at the time of inclusion. 
**Implementation**: Before a trade is authorized, the system must check the volatility reference indicator of the said asset and make sure it is included in the specified [x;y] range. 
The volatility reference indicator could be standard deviation but also downside deviation. Possible relevant indicators: standard deviation, Sharpe ratio, Sortino indicator.
**Parameters**: x as low boundary of the acceptable volatility range and y as high boundary of the acceptable volatility range.

Portfolio base currency cannot be considered for this rule.

#### Rule 11: Portfolio volatility threshold
**Definition**: A fund is prevented from performing a trade if the effect on the portfolio volatility is undesirable (ie if volatility post trade exceeds X% level of volatility)
**Rationale**: Investors have an understanding of the volatility level of the portfolio maintained at the time of trading.
**Implementation**: Before a trade is authorized, the system must check the post volatility of the total portfolio and make sure it is included in the specified [x;y] range.
**Parameters**: x as low boundary of the acceptable portfolio volatility range and y as high boundary of the acceptable portfolio volatility range.
**Side note**: This computation may be too expensive on Ethereum smart contract. We might be able to use a simplified computation. But this rule is to be envisioned in the Melon chain context.

Edge case: Could also allow trades which incrementally reduce (increase) portfolio volatility. Basically, is the trade beneficial to the investor and in the spirit of the guidelines.

#### Rule 12: Liquidity 
**Definition**: A fund can only invest in assets whose liquidity is contained in a certain range [x;y] (ie x < liquidity < y).  
**Rationale**: (i) enforce strategy focus
**Implementation**: Before a trade is authorized, the system must check the liquidity available for that asset and make sure it is included in the specified [x;y] range. The liquidity data is retrieved from the exchanges the fund is allowed to trade on. Liquidity can be expressed as the % of your trade volume over the 30-days ADV (average daily volume traded).
**Parameters**: x as low boundary and y as high boundary of range.

#### Rule 13: Correlation of adding an asset to portfolio 
**Definition**: A fund can invest in an asset if its correlation with the current portfolio is contained between a range [x;y]
**Rationale**: (i) making sure that adding an asset helps optimizing the portfolio or at least move it in the right direction.
**Implementation**: Before a trade is authorized, the system must compute the correlation of that asset to the portfolio and make sure it is included in the specified [x;y] range. 
**Parameters**: x as low boundary and y as high boundary of the correlation range.

#### Rule 14: Time horizon
**Definition**: A fund manager needs to hold an asset for specific average period of time.
**Implementation**: We voluntarily decide not to encode that rule in a smart contract as it can lead to dangerous situations for the fund. Could be as a guideline for informational purposes but won't be enforced. 

(not seen in practice, could be gamed by investor redemption and subsequent re-investment; must be thoughtfully alligned with lockup.)

#### Rule 15: Full investment - (Remove, unimportant; could cause sub-optimal behavior - keep as indicator for reporting?)
**Definition**: A fund manager can defined the full investment treshold ie. the proportion of assets invested vs the proportion of cash held. 
**Rationale**: The fund manager should not stay passive collecting management fees just by holding too large proportion of cash (taking no risk) risk. 
**Implementation**: We do not want to enforce that rule as the fund manager should have the flexibility to put everything in safety in case of imminent market crash or similar event. It could simply be an informational rule that can be used in monitoring/reporting tool to compare with actual investment levels of the fund. 
**Parameters**: X as the percentage of NAV invested.

#### Rule 16: Generalizable 5/10/40 rule.
This is a current UCITS rule. Positions may not exceed 10% of NAV, and that assets which exceed 5% of NAV summed together may not exceed 40% as a group.

#### Rule 17: Specific Asset/Category Max (%)
This rule assigns a maximum allocation as a percentage of NAV to a specific token or category. This is slightly different from Rule 4 which is an unspecified, blanket maximum allocation for any specific asset token or category. 

#### Rule 18: Fund Cap?

-----

### Summary Matrix

This table summaraizes the Risk Engineering rule set and their applicability to specific chain implementation. All Risk Engineering rules are implemented/configured at the discretion of the Investment Manager (IM) at the time of fund (smart contract) creation. The fund itself should be seen as the bare minimum functionality to support required fund, perfomrance and share accounting functionality. Risk Engineering functionaltiy should be viewed as optional plugins for the fund to provide simplified ability to steer the construction of the fund and its underlying holdings.

| Risk Engineering Rule | Ethereum | MelonChain | Parameters | Mutable ex post|
| -------- | -------- | -------- | -------- | -------- |
| 1. Max Positions     |    ✓     |     ✓     | positions : integer (>0)| No 
| 2. Whitelist     |    ✓     |     ✓     | addr : address | Delete-only
| 3. Blacklist     |    ✓     |     ✓     | addr : address | Append-only
| 4. Max Concentration (%)     | ✓     | ✓     |percent : integer   ∈ [1,100]| No
| 5. Lock Up     | ✓     | ✓     | blocks : integer |No
| 6. Best Price Execution Tolerance (%)     | ✓     | ✓     | percent : integer ∈ [1,100]| No
| 7. Turnover (# of trades)    |  ✓    | ✓     | trades : integer (>0), period : {hour, day, week, ?blocks}| No
| 8. Turnover (volume of trades)     |  ✓    | ✓     | trade volume : integer (>0), period : {hour, day, week, ?blocks}| No
| 9. Market Cap Range     | ✓     | ✓     |lower : integer (>0), upper : integer (>0) | No
| 10. Asset Vola Threshold     | ✓     | ✓     |percent : integer ∈ [1,100]| No
| 11. Portfolio Vola Threshold | ✓     | ✓     |percent : integer ∈ [1,100]| No
| 12. Liquidity     | ✓     | ✓     |
| 13. Asset <-> Portfolio Correlation     | ✓     | ✓     |

