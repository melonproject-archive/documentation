# Risk Policies

## General

## Risk Management vs Risk Engineering

*Risk* is the effect of uncertainty on objectives; the risk is the possibility that an event will occur and adversely affect the achievement of an objective.

Risk is uncertainty with implicit consequences. In general, the investor cares about two types of risk: 1) those for which she receives compensation in return for bearing, and 2) those for which no compensation is received.  It is obvious that the latter must be eliminated to the extent possible.

For example, the following shows risk dissected into systematic risk and unsystematic risk:

![Related image](https://www.tytoncapital.com/wp-content/uploads/2016/05/types-of-risk.gif)

We can observe that unsystematic risk can be *completely* eliminated from the portfolio by sufficiently diversifying or simply having a sufficient number of assets in the portfolio. Of course, these assets must be reasonably uncorrelated with each other.

Critically, an investor is NOT compensated for bearing  unsystematic risk, and this would be detrimental to the portfolio's return (and the investor's wealth) over time.

## Ex Post -> "Management"
Risks which have materialized should be managed to the extent possible. Examples:
- A position in the portfolio experiences positive returns to the point that the allocation breaches a concentration guideline. Managing this concentration risk means liquidating a portion of the position to return the position to a tolerable percentage of the portfolio. The proceeds are then allocated to other existing or new positions at the discretion of the investment manager.
- A position's price volatility increases to the point that it becomes too "risky" for the portfolio. This could be regarding the allocation or in general. In the former case, the position would be reduced so that the asset-weighted volatility of the portfolio returns to tolerable levels

## Ex Ante -> "Engineering"
There are potential risks that we can explicitly prohibit with blockchain tooling. This is where we want to shine.

Portfolio guidelines are rules which are laid out in a legal document (Offering Memorandum or Prospectus) prior to fund inception. Here, the parties agree on the types of instruments to be invested, strategy, rules about concentration and other rule-based metrics.

All institutional fund managers use software to help them manage portfolios.  They will say, "Our software will not allow a trade which conflicts with the guidelines", or they will have a process in place which has a name like "pre-trade clearance" or "hypothetical trading". These methods have served the industry to a satisfactory level, but non-compliant trades do find their way into a portfolio.

Melon funds approach risk differently. Melon fund currently implement *risk engineering* i.e. anticipating a specific risk and handling it in the code of the smart contract. Essentially, this is a proactive posture, as opposed to a reactive posture.

*Risk engineering* is the anticipation and identification of different risks, and the action of preventing their occurrence through code. Rules coded into smart contracts that are meant to ensure that something **cannot not** happen in the structure of a Melon fund.

# Implemented Risk Policies

## AssetBlacklist.sol

#### Description

The Policy explicitly prohibits the Melon fund from investing in or hold any asset token which is part of the contract's blacklist. The rationale for this Policy my be: (i) investors have a guarantee that the fund will never invest in an asset. (ii) explicit restrictions on the manager (iii) regulatory compliance (iv) investor preferences. Assets token addresses can be added, but not removed from the contract's blacklist.
&nbsp;

#### Inherits from

Policy, AddressList (Link)

&nbsp;

#### On Construction

The contract requires an array of addresses, where each address is added to the contract's internal list.

&nbsp;

#### Structs

None.

&nbsp;

#### Enums

None.

&nbsp;

#### Modifiers

None.

&nbsp;

#### Events

None.

&nbsp;

#### Public State Variables

None.

&nbsp;

#### Public Functions

`function addToBlacklist(address _asset) external auth`

This function requires that the caller is the `owner` or the current contract. The function then requires that the token asset address is not a member of the list. If it is not a member of the list, the address provided is then added to the contract's internal list. Once a token asset address is blacklisted, it cannot be removed.
&nbsp;

`function rule(bytes4 sig, address[5] addresses, uint[3] values, bytes32 identifier) external view returns (bool)`

This view function returns `true` if the taker asset address (position [3] in the `addresses` array parameter) is not a member of the blacklist. The function returns `false` if the taker asset is a member of the blacklist. For a full description of the parameters, please refer to `rule()` in the general Policy contract above.
&nbsp;

`function position() external view returns (Applied)`

This view function returns the enum `pre`, indicating the the Policy logic be evaluated prior to execution of the Policy's corresponding registered function call.
&nbsp;

## AssetWhitelist.sol

#### Description

The Policy explicitly permits the Melon fund to invest in and hold any asset token which is part of the contract's whitelist as defined at fund setup. The rationale for this Policy my be: (i) Investors have a clear understanding of which assets can ever be invested in by the fund. (ii) restricting the investment manager - tightening the scope of the investment universe. (iii) the fund can only invest in a niche classification (eg. ESG, utility tokens, virtual currencies, commodities only etc.). Assets token addresses can be removed from, but not added to the contract's whitelist.
&nbsp;

#### Inherits from

Policy, AddressList (Link)

&nbsp;

#### On Construction

The contract requires an array of addresses, where each address is added to the contract's internal list.

&nbsp;

#### Structs

None.

&nbsp;

#### Enums

None.

&nbsp;

#### Modifiers

None.

&nbsp;

#### Events

None.

&nbsp;

#### Public State Variables

None.

&nbsp;

#### Public Functions

`function removeFromWhitelist(address _asset) external auth`

This function requires that the caller is the `owner` or the current contract. The function then requires that the token asset address is a member of the contract's list. If it is a member of the list, the address provided is then removed from the contract's internal list. Whitelisted token asset addresses can be removed from, but not added to, the contract's list.
&nbsp;

`function rule(bytes4 sig, address[5] addresses, uint[3] values, bytes32 identifier) external view returns (bool)`

This view function returns `true` if the taker asset address (position [3] in the `addresses` array parameter) is a member of the whitelist. The function returns `false` if the taker asset is not a member of the whitelist. For a full description of the parameters, please refer to `rule()` in the general Policy contract above.
&nbsp;

`function position() external view returns (Applied)`

This view function returns the enum `pre`, indicating the the Policy logic be evaluated prior to execution of the Policy's corresponding registered function call.
&nbsp;


## MaxConcentration.sol

#### Description

The Policy explicitly prevents a position from actively exceeding the specified percentage of fund value. The rationale for this Policy is to: (i) avoid cluster risk, (ii) aid portfolio diversification and (iii) reduce unsystematic risk. This Policy is evaluated after execution of the registered corresponding function.
&nbsp;

#### Inherits from

DSMath, Policy (Link)

&nbsp;

#### On Construction

The contract requires the parameter representing the concentration upper limit as a percentage be less than 100%. The public state variable `maxConcentration` is then set to the value provided by this parameter.
&nbsp;

#### Structs

None.

&nbsp;

#### Enums

None.

&nbsp;

#### Modifiers

None.

&nbsp;

#### Events

None.

&nbsp;

#### Public State Variables

`uint public maxConcentration`

This public variable stores the upper limit, in percentage of fund value, which an individual asset token position can have. The value is stored using 18 decimal precision, i.e. 100000000000000000 would represent a maximum concentration for any individual token asset position of 10% of fund value.
&nbsp;

#### Public Functions

`function rule(bytes4 sig, address[5] addresses, uint[3] values, bytes32 identifier) external view returns (bool)`

This view function returns `true` if Melon fund's quote asset is the taker asset. The rationale for this is that quote asset should not be subject to the maximum concentration rule as the quote asset represents non-exposure to the wider market. In cases where the taker asset is not the quote asset, the function evaluates the post-trade allocation percentage of the position to fund value, returning `true` if the entire fund post-trade position in the taker asset does not exceed the maximum concentration percentage. If the fund's post-trade position in the taker asset does exceed the maximum concentration percentage, the function returns `false` and the transaction is reverted. For a full description of the parameters, please refer to `rule()` in the general Policy contract above.
&nbsp;

`function position() external view returns (Applied)`

This view function returns the enum `post`, indicating the the Policy logic be evaluated after execution of the Policy's corresponding registered function call.
&nbsp;


## MaxPositions.sol

#### Description

The Policy explicitly prevents a position from entering the fund if the quantity of non-quote asset token positions exceed the specified `maxPositions` state variable. The rationale for this is to: (i) avoid over-diversification and (ii) avoid excessive rebalancing transactions in the event of a new subscription (or redemption). This Policy is evaluated after execution of the registered corresponding function.
&nbsp;

#### Inherits from

Policy (Link)

&nbsp;

#### On Construction

The contract requires a parameter representing the maximum number of non-quote asset token positions permitted in the Melon fund. The public state variable `maxPositions` is then set to the value provided by this parameter. A value of 0 means that no non-quote assets positions are permitted.
&nbsp;

#### Structs

None.

&nbsp;

#### Enums

None.

&nbsp;

#### Modifiers

None.

&nbsp;

#### Events

None.

&nbsp;

#### Public State Variables

`uint public maxPositions`

This public variable stores the upper limit of the number of non-quote asset positions permitted in the Melon fund. For example, `maxPositions` == 5 means that a maximum of five non-quote asset token positions are permitted at any time.
&nbsp;

#### Public Functions

`function rule(bytes4 sig, address[5] addresses, uint[3] values, bytes32 identifier) external view returns (bool)`

This view function returns `true` if Melon fund's quote asset is the taker asset. The rationale for this is that quote asset should not be subject to the maximum positions rule as the quote asset represents non-exposure to the wider market. In cases where the taker asset is not the quote asset, the function evaluates the post-trade number of non-quote asset positions in the fund, returning `true` if the  post-trade number of non-quote asset positions in the fund does not exceed the maximum positions configuration. If the fund's post-trade position in the taker asset causes the fund's non-quote asset position quantity to exceed the maximum positions quantity, the function returns `false` and the transaction is reverted. For a full description of the parameters, please refer to `rule()` in the general Policy contract above.
&nbsp;

[CHECK]
accounting.getFundHoldingsLength() <= maxPositions;
-->
accounting.getFundHoldingsLength()-1 <= maxPositions;
&nbsp;


`function position() external view returns (Applied)`

This view function returns the enum `post`, indicating the the Policy logic be evaluated after execution of the Policy's corresponding registered function call.
&nbsp;

## PriceTolerance.sol

#### Description

The PriceTolerance Policy explicitly prevents a make order from being submitted to an exchange, or explicitly prevents a take order trade from executing depending on a comparison between the implicit order price, adjusted for the specified tolerance, and the current reference price as provided by the price feed. The rationale is that the Policy: (i) prevents predatory investment manager behavior and (ii) mitigates wash trading. This Policy is evaluated prior to execution of the registered corresponding functions.
&nbsp;

#### Inherits from

Policy, DSMath (Link)

&nbsp;

#### On Construction

The contract requires a parameter representing the maximum price feed deviation of an asset trade to the detriment of fund investors. The `tolerance` state variable is then set to this value. A parameter value of 10 would represent a maximum order price deviation to the current reference price of 10%.
&nbsp;

#### Structs

None.

&nbsp;

#### Enums

None.

&nbsp;

#### Modifiers

None.

&nbsp;

#### Events

None.

&nbsp;

#### Public State Variables

`uint tolerance`   [CHECK public?]

This state variable stores the permitted deviation in percentage of the order price to the current reference price. A value of 10 would represent a maximum order price deviation to the current reference price of 10%.
&nbsp;

#### Public Functions

`function takeOasisDex(address ofExchange, bytes32 identifier, uint fillTakerQuantity) view returns (bool)`

This view function gathers data from the existing Oasis Dex order and respective amounts filled, then compares the resulting price with the tolerance percentage-adjusted reference price from the price feed. If the order price does not exceed the tolerance percentage-adjusted reference price, the function returns `true` and the fund is permitted to take the order (partial- or complete fill); otherwise the function returns `false`, the trade is not permitted and the transaction is reverted.
&nbsp;

`function takeGenericOrder(address makerAsset, address takerAsset, uint[3] values) view returns (bool)`

This view function accommodates generic exchange take order functionality, calculating the maker fill quantity given the taker quantity, maker quantity and the taker fill quantity, then compares the resulting price (fill quantity ratio) with the tolerance percentage-adjusted reference price from the price feed. If the take order price does not exceed the tolerance percentage-adjusted reference price, the function returns `true` and the fund is permitted to take the order (partial- or complete fill); otherwise the function returns `false`, the trade is not permitted and the transaction is reverted.
&nbsp;

`function takeOrder(address[5] addresses, uint[3] values, bytes32 identifier) public view returns (bool)`

This view function is evaluated prior to executing take orders on exchanges and determines the execution routing for take orders depending on value of the `identifier` parameter. If the parameter `identifier == 0x0` (denoting a 0x-integrated exchange), the function calls `takeGenericOrder()` and `takeOasisDex()` otherwise, passing all input parameters on to the subsequent function call.
&nbsp;

`function makeOrder(address[5] addresses, uint[3] values, bytes32 identifier) public view returns (bool)`

This view function is evaluated prior to executing make orders on exchanges. The function determines the current reference price for the asset token pair, then determines the price as determined by the make offer quantities.  If the make order price does not exceed the tolerance percentage-adjusted reference price, the function returns `true` and the fund is permitted to submit the make order to the exchange; otherwise the function returns `false`, the make order is not permitted and the transaction is reverted.
&nbsp;

`function rule(bytes4 sig, address[5] addresses, uint[3] values, bytes32 identifier) external view returns (bool)`

This view function is evaluated prior to executing make orders or take orders on exchanges and determines the execution routing for these order types depending on value of the `sig` parameter, which is the keccak256 hash of the plain text function signature of `makeOrder()` or `takeOrder()`. The function returns `true` for permitted orders, i.e. make orders can be submitted to the exchange and take orders can be traded. The function returns `false` for prohibited orders which violate the price tolerance criteria, reverting the transaction. For a full description of the parameters, please refer to `rule()` in the general Policy contract above.
&nbsp;

`function position() external view returns (Applied)`

This view function returns the enum `pre`, indicating the the Policy logic be evaluated prior to execution of the Policy's corresponding registered function call.
&nbsp;
