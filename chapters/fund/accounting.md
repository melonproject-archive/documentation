# Accounting

### General

### Accounting.sol

#### Description

The Accounting contract defines the accounting rules implemented by the fund. All operations concerning the underlying fund positions, fund position maintenance, asset token pricing, fees, Gross- and Net Asset value calculations and per-share calculations come together in this contract's business logic.

#### Inherits from
Spoke, DSMath (links)

#### On Construction

The contract requires the hub address, the quote asset address and an array of default asset addresses. These inputs set the accounting spoke's quote asset, share decimals (18), the default share price (1.0 in quote asset terms) and add all default assets passed in to the `ownedAssets` array state variable.

The Accounting contract is created from the AccountingFactory contract, which creates a new instance of `Accounting` given the `hub` address, registering the address of the newly created Accounting contract as a child of the AccountingFactory.


#### Structs

`Calculations`

  Member Variables:

  `uint gav` - The Gross Asset Value of all fund positions  
  `uint nav` - The Net Asset Value of all fund positions  
  `uint allocatedFees` - Fee shares accrued since the previous fee calculation about to be allocated
  `uint totalSupply` - The quantity of fund shares  
  `uint timestamp` - The timestamp of the current transactions block


#### Enums

None.

#### Modifiers

None.


#### Public State Variables

`address[] public ownedAssets`

A pubic array containing the addresses of tokens currently held by the fund.


`mapping (address => bool) public isInAssetList`

A mapping defining the status of an asset's membership in the `ownedAssets` array.


`address public QUOTE_ASSET`

The address of the token defined to be the quote asset, or base currency of the fund. NAV, performance and all fund-level metrics will be denominated in this asset. One unit of the quote asset.


`uint public DEFAULT_SHARE_PRICE`

An integer determining the initial "sizing" of one share in the fund relative to the quote asset. The share price at fund inception will always be one unit of the quote asset. *rename to INCEPTION_SHARE_PRICE*, *should this be configureable?*


`uint public SHARES_DECIMALS`

An integer determining the number of decimals, or the degree of divisibility, of a fund share. This value is set to 18, which is congruent with the divisibility of ETH and many other tokens. [CHECK Also stored in shares.sol]


`Calculations public atLastAllocation`

A `Calculations` structure holding the latest state of the member fund calculations described above.


#### Public functions

`function getFundHoldings() returns (uint[], address[])`

This function returns the current quantities and corresponding addresses of the funds token positions as two distinct order-dependent arrays.


`function getFundHoldingsLength() view returns (uint)`

This view function returns the number of distinct token assets held by the fund.


`function calcAssetGAV(address ofAsset) returns (uint)`

This function calculates and returns the current fund position GAV (in quote asset terms) of the individual asset token as specified by the address provided.


`function assetHoldings(address _asset) public returns (uint)`

This function returns the fund position quantity of the asset token as specified by the address provided.


`function calcGav() public returns (uint gav)`

This function calculates and returns the current Gross Asset Value (GAV) of all fund assets in quote asset terms.


`function calcUnclaimedFees(uint gav) view returns (uint)`

This function calculates and returns all fees due to the manager (in share terms) at the time of execution given the fund Gross Asset Value (GAV).


`function calcNav(uint gav, uint unclaimedFees) pure returns (uint)`

This function calculates and returns the fund's Net Asset Value (NAV) given the provided fund GAV and current quantity of unclaimed fee shares.


`function calcValuePerShare(uint totalValue, uint numShares) view returns (uint)`

This function calculates and returns the value (in quote asset terms) of a single share in the fund given the fund total value and the total number of shares.


`function performCalculations() view
returns (uint gav, uint unclaimedFees, uint feesShareQuantity, uint nav, uint sharePrice)`

This view function returns bundled calculations for GAV, NAV, unclaimed fees, fee share quantity and current share price (in quote asset terms).


`function calcSharePrice() view returns (uint sharePrice)`

This function calculates and returns the current price (in quote asset terms) of a single share in the fund.


`function calcSharePriceAndAllocateFees() public returns (uint)`

This function calculates and returns the price of a single share (in quote asset terms) as well as performing the fee share calculation and allocation.


`function updateOwnedAssets() public`

This function maintains the `ownedAssets` array and the `isInOwedAssets` mapping by removing or adding asset addresses as the fund holdings composition changes.


`function addAssetToOwnedAssets(address _asset) public auth`

This function requires that the caller is the `owner` or the current contract. The function maintains the `ownedAssets` array and the `isInOwedAssets` mapping by adding asset addresses as the fund holdings composition changes.


`function removeFromOwnedAssets(address _asset) public auth`

This function requires that the caller is the `owner` or the current contract. The function maintains the `ownedAssets` array and the `isInOwedAssets` mapping by removing asset addresses as the fund holdings composition changes.
