# Accounting

## `Accounting.sol`

Description

Contract that defines the accounting rules implemented by the fund.

Inherits from Spoke, DSMath (links)

On Construction

The contract requires the hub address, the quote asset address and an array of default asset addresses. These inputs set the accounting spoke's quote asset, share decimals (18), the default share price (1.0 in quote asset terms) and add all default assets passed in to the `ownedAssets` array state variable.


Structs

`Calculations`

    Member Variables

    `uint gav` - The Gross Asset Value of all fund positions  
    `uint nav` - The Net Asset Value of all fund positions  
    `uint allocatedFees`  
    `uint totalSupply` - The quantity of fund shares  
    `uint timestamp` - The timestamp


Enums
None.


Public State Variables

`address[] public ownedAssets`

A pubic array containing the addresses of tokens currently held by the fund.


`mapping (address => bool) public isInAssetList`

A mapping defining the status of an asset's membership in the `ownedAssets` array.


`address public QUOTE_ASSET`

The address of the token defined to be the quote asset, or base currency of the fund. NAV, performance and all fund-level metrics will be denominated in this asset. One unit of the quote asset.


`uint public DEFAULT_SHARE_PRICE`

An integer determining the initial "sizing" of one share in the fund relative to the quote asset. The share price at fund inception will always be one unit of the quote asset. *rename to INCEPTION_SHARE_PRICE*, *should this be configureable?*


`uint public SHARES_DECIMALS`

An integer determining the number of decimals, or the degree of divisibility, of a fund share. This value is set to 18, which is congruent with the divisibility of ETH and many other tokens.


`Calculations public atLastAllocation`

A Calculations structure holding the latest state of the member fund calculations described above.


Public functions

`function getFundHoldings() returns (uint[], address[])`

This function returns the current quantities and corresponding addresses of the funds token positions.


`function getFundHoldingsLength() view returns (uint)`

This function returns the number of distinct token assets held by the fund.


`function calcAssetGAV(address ofAsset) returns (uint)`

This function calculates the current GAV (in quote asset terms) of the asset token as specified by the address.


`function assetHoldings(address _asset) public returns (uint)`

This function returns the quantity of the asset token as specified by the address.


`function calcGav() public returns (uint gav)`

This function calculates and returns the current Gross Asset Value (GAV) of the fund in quote asset terms.


`function calcUnclaimedFees(uint gav) view returns (uint)`

This function calculates and returns fees due to the manager (in share terms) given the fund Gross Asset Value (GAV).


`function calcNav(uint gav, uint unclaimedFees) pure returns (uint)`

This function calculates and returns the Net Asset Value (NAV) given the GAV and currently unclaimed fees.


`function calcValuePerShare(uint totalValue, uint numShares) view returns (uint)`

This function calculates and returns the value (in quote asset terms) of a single share in the fund given the fund total value and the total number of shares.


`function performCalculations() view
returns (uint gav, uint unclaimedFees, uint feesShareQuantity, uint nav, uint sharePrice)`

This function bundles and returns calculations for GAV, NAV, unclaimed fees, fee share quantity and current share price (in quote asset terms).


`function calcSharePrice() view returns (uint sharePrice)`

This function calculates and returns the current price (in quote asset terms) of a single share in the fund.


`function calcSharePriceAndAllocateFees() public returns (uint)`

This function calculates and returns the price of a single share (in quote asset terms) as well as performing the fee share calculation and allocation.


`function updateOwnedAssets() public`

This function maintains the `ownedAssets` array and the `isInOwedAssets` mapping by removing or adding asset addresses as the fund holdings composition changes.


`function addAssetToOwnedAssets(address _asset) public auth`

This function maintains the `ownedAssets` array and the `isInOwedAssets` mapping by adding asset addresses as the fund holdings composition changes.


`function removeFromOwnedAssets(address _asset) public auth`

This function maintains the `ownedAssets` array and the `isInOwedAssets` mapping by removing asset addresses as the fund holdings composition changes.


Document what auth is...
