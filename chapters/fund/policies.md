

# Policies

## General

Polices are individual smart contracts which define rule or set of rules to be compared to the state of the Melon fund. Policies simply assess the current state of the Melon fund and resolve to a boolean decision, whether the action may be executed or not, returning `true` for allowed actions and `false` for disallowed actions. The Melon fund-specific Policies are deployed with parameterized values which the defined Policy logic uses to assess the permissibility of the action.

#### Pre- and Post-Conditionality
Policies are intended to be rules; they are intended to permit or prevent specific behavior or action depending on the *state* as compared to specified criteria.

Some Policies, due to the nature of the data required, can be immediately resolved based on the *current* state. The resolution of the Policy result is trivial because all data needed is at hand and must nod be derived or calculated. Such Policies can be defined as "pre-condition" Policies.

Other Policies may need to assess the consequence of the behavior or action before the logic can assess its permissibility relative to the defined rule. To do this, the result of the action must be derived or calculated, essentially asking, "What *will* the state be if this action is executed?" Such Policies can be defined as "post-condition" Policies.

On the blockchain and in smart contracts, we can use a fortunate side-effect of the process of mining and block finalization to help determine the validity of post-condition Policies. With post-condition Policies, the action or behavior is executed with the smart contract logic and the changed (but not yet finalized or mined) state is assessed against the logic and defined parameters of the post-condition Policy. In the case where this new state complies with the logic and criteria of the Policy, the action is allowed, meaning the smart contract execution is allowed to run to completion, the block is eventually mined and this new compliant state is finalized in that mined block. In the case where the new state does not comply with the logic and criteria of the Policy, the action is disallowed and the revert() function is called, stopping execution and discarding (or rolling back) all state changes. In calling the revert() function, gas is consumed to arrive at the reference state, but any unused gas is returned to the caller as the reference state is discarded.
&nbsp;


## Policy.sol

#### Description

The Policy contract is inherited by implemented Policies. This contract will be changed to an interface when upgraded to Solidity 0.5, as enums and structs are allowed to be defined in interfaces in that version.
&nbsp;

#### Inherits from

None.

&nbsp;

#### On Construction

None.

&nbsp;

#### Structs

None.

&nbsp;

#### Enums

`Applied` - An enum which characterizes the conditionality type of the Policy.

  Member Types

  `pre` - Indicates that the Policy will be evaluated prior to the corresponding function's execution.

  `post` - Indicates that the Policy will be evaluated after the corresponding function's execution.
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

`function rule(bytes4 sig, address[5] addresses, uint[3] values, bytes32 identifier) external view returns (bool)`

This view function is called by the PolicyManager to ensure that specific registered function calls are validated by their respective registered Policy validation logic. The function returns `true` when conditions defined in the Policy are met and execution continues to successful completion. If conditions defined in the Policy are no met, the function returns `false`, all execution up to that point is reverted and execution cannot complete, returning all remaining gas.

The `rule()` function takes a function signature hash `sig`, an array of address `addresses`, an array of uint `values` and an `identifier` are passed as parameters. For the arrays, the order of the address or value in the array is semantically significant. The individual array positions, [n], are defined as follows:

`address[5] addresses`:

[0] order maker - the address initiating or offering the trade
[1] order taker - the address filling or partially filling the offered trade
[2] maker asset - the token address of the asset token intended by the order maker to exchange for another token asset, i.e. the taker asset
[3] taker asset - the token address of the asset token to be received by the offer maker in exchange for the maker asset
[4] exchange address - address of the exchange contract on which the order is to be placed

`uint[3] values`:

[0] maker token quantity - the maximum quantity of the maker asset token to be given by the order maker in exchange for the specified taker asset token
[1] taker token quantity - the maximum quantity of the taker asset token to be received by the order maker in exchange for the specified maker asset token
[2] Fill amount - the quantity of the taker token exchanged in the transaction. Must be less than or equal to the taker token quantity [1]

Finally, `identifier` and `sig` parameters are described below:

`bytes32 identifier` - order id for exchanges utilizing a unique, exchange-specific order identifier

`bytes4 sig` - the keccak256 hash of the plain text function signature of the function which triggers the specific policy validation.

&nbsp;

`function position() external view returns (Applied)`

This view function returns the enum `Applied` which is defined for each specific Policy contract. The `Applied` enum indicates whether the Policy logic is applied prior to, or after the corresponding function's execution. This function is called when the Policy contract is registered with the PolicyManager contract.

## Implemented Policies

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
