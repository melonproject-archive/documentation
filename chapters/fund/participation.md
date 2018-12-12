# Participation

## Participation.sol

#### Description

The Participation contract encompasses the entire Melon fund interface to the investor and manages or delegates all functionality pertaining to fund subscription and redemption activities including the creation/destruction of Melon fund shares, fee calculations, asset token transfers and enabling/disabling specific asset tokens for subscription.

#### Inherits from

DSMath, AmguConsumer, Spoke (links)

#### On Construction

The contract requires the hub address and an array of default asset addresses. These inputs set the hub and enable investment for the assets passed in to the constructor.

The Participation contract is created from the ParticipationFactory contract, which creates a new instance of `Participation` given the `hub` address, registering the address of the newly created Participation contract as a child of the ParticipationFactory.



#### Structs

`Request`

  Member variables:

  `address investmentAsset` - The address of the asset token with which the subscription is made.
  `uint investmentAmount` - The quantity of tokens of the `investmentAsset`
  `uint requestedShares` - The quantity of fund shares for the request
  `uint timestamp` - The timestamp of the block containing the subscription transaction
  `uint atUpdateId` - [CHECK]


#### Enums

None.

#### Modifiers

None.


#### Events

`RequestExecuted()`

    `address investmentAsset` - The address of the asset token with which the subscription is made.
    `uint investmentAmount` - The quantity of tokens of the `investmentAsset`
    `uint requestedShares` - The calculated quantity of fund shares created for the subscription
    `uint timestamp` - The timestamp of the block containing the subscription transaction
    `uint atUpdateId` - [CHECK]

This event is triggered when a subscription is successfully executed by the fund.

`SuccessfulRedemption()`

    `uint quantity` - The quantity of shares successfully redeemed

This event is triggered when a redemption is successfully executed by the fund.


#### Public State Variables

`mapping (address => Request) public requests`

A public mapping which assigns an investor's address to a `Request` struct.

`mapping (address => bool) public investAllowed`

A public mapping which specifies all asset tokens which have been enabled for subscription to the Melon fund.

`uint public SHARES_DECIMALS`

An integer which specifies the decimal precision of a single share. The value is set to 18.



#### Public Functions

`function enableInvestment(address[] _assets) public auth`

This function requires that the caller is the `owner` or the current contract. The function iterates over an array of provided asset token addresses, ensuring each is registered with the Melon fund's PriceFeed, and ensures that registered asset token addresses are set to `true` in the `investAllowed` mapping.


`function disableInvestment(address[] _assets) public auth`

This function requires that the caller is the `owner` or the current contract. The function iterates over an array of provided asset token addresses and ensures that asset token addresses are set to `false` in the `investAllowed` mapping.


`function requestInvestment(uint requestedShares, uint investmentAmount, address investmentAsset) external payable amguPayable`

This function ensures that the fund is not shutdown and that subscription is permitted in the provided `ìnvestmentAsset`. The function then creates and populates a `Request` struct (see details above) from the function parameters provided and adds this to the `requests` mapping corresponding to the `msg.sender`. Execution of this functions requires payment of AMGU ETH to the Melon Engine. [CHECK: Further explantation and detail] [CHECK: add compliance when implemented]


`function cancelRequest() external`  

This function removes the request from the `requests` mapping for the request corresponding to the `msg.sender` address.


`function executeRequest() public payable`  

This function calls `executeRequestFor()` on behalf of `msg.sender`.


`function executeRequestFor(address requestOwner) public`

This function:
  ensures that the fund is not shutDown,
  ensures that the investor address is permitted to carry out the action,
  ensures that a request corresponding to `msg.sender` exists in the `requests` mapping,
  ensures that subscription is permitted in the subscribing asset token,
  ensures that the price for the subscribing asset token is recent,
  ensures that management fee shares have been calculated and allocated immediately prior to subscription,
  calculates the cost of the share quantity requested (in quote asset terms),
  ensures that the cost of the share quantity requested is sufficiently covered by the `investmentAmount`,
  maintains the `requests` mapping by removing the current `Request` corresponding to `msg.sender`,
  ensures the transfer of the subscription tokens to the fund's vault,
  creates and transfers/allocates the commensurate number of shares for the subscription to the `msg.sender` (investor) address,
  determines if the asset is currently held by the fund; if not, the asset is added to the fund's owned assets,
  and finally, the function emits the RequestExecuted() event, broadcasting the event's parameters (see event specification above). The function reverts all work if any permission, quantity, asset or address are determined to be invalid.


`function getOwedPerformanceFees(uint shareQuantity) view returns (uint remainingShareQuantity)`

  This view function calculates and returns the quantity of shares owed for payment of accrued performance fees given the provided quantity of shares being redeemed.


`function redeem() public`

This function determines the quantity of shares owned by `msg.sender` and calls `redeemQuantity()`. A share-commensurate quantity of all token assets in the fund are transferred to `msg.sender`, i.e. the investor. This function redeems *all* shares owned by `msg.sender`.


`function redeemQuantity(uint shareQuantity) public`

This function allows the investor to redeem a specified quantity of shares held by the `msg.sender`, i.e. the investor.


`function redeemWithConstraints(uint shareQuantity, address[] requestedAssets) public`

This function:
  ensures that the requested redemption share quantity is less than or equal to the share quantity owned by the investor (`msg.sender`),
  ensures that management fee shares have been calculated and allocated immediately prior to redemption,
  ensures that accrued performance fee shares have been calculated, allocated to the manager, removed from the investor's balance and added to total share supply immediately prior to redemption,
  maintains parity between `requestedAssets[]` array parameter and the `redeemedAssets[]`  internal array, ensuring that individual asset tokens are only redeemed once,
  calculates the proportionate quantity of each token asset owned by the investor (`msg.sender`) based on share quantity,
  destroys the investor's shares and reduces the fund's total share supply by the same amount,
  safely transfers all token assets in the correct quantities to the `msg.sender` (investor) address using the ERC20 `transfer()` function,
  and finally, the function emits the `SuccessfulRedemption()` event along with the quantity of successfully redeemed shares.


`function hasRequest(address _who) view returns (bool)`

This view function returns a boolean indicating whether the provided address has a corresponding active request with a positive quantity of requested shares in the `requests` mapping.
