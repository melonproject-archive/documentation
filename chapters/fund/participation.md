# Participation

## Participation.sol

Description

...

Inherits from Spoke, DSMath (links)

On Construction
The contract becomes a spoke of the hub.


Structs

`Request`

    Member variables:

    `address investmentAsset`
    `uint investmentAmount`
    `uint requestedShares`
    `uint timestamp`
    `uint atUpdateId`


Enums
None.


Public State Variables

`mapping (address => Request) public requests`

A mapping which assigns an investor's address to a `Request` struct.


`uint public SHARES_DECIMALS`

An integer which specifies the number of decimals or divisibility of a single share.



Public Functions

`function requestInvestment(uint requestedShares, uint investmentAmount, address investmentAsset) external`

This function ensures that the fund is not shutdown, then populates the `Request` struct and adds this to the `requests` mapping corresponding to the `msg.sender`.  This function also sets `SHARE_DECIMALS` to 18.


`function cancelRequest() external`  

This function removes the request from the `requests` mapping.


`function executeRequest() public`  

This function calls `executeRequestFor()` on behalf of `msg.sender`.


`function executeRequestFor(address requestOwner) public`

This function:
  ensures that the fund is not shutDown,
  ensures that the investor address is permitted to carry out the action,
  ensures the quantity of requested shares is greater than zero,
  calculates the cost of the share quantity requested (in quote asset terms),
  ensures that the cost of the share quantity does not exceed the requested share quantity,
  maintains the `requests` mapping by removing the current `Request`,
  transfers the subscription tokens to the fund's vault,
  creates and transfers/allocates the commensurate number of shares for the subscription to the investor,
  determines if the asset is currently held by the fund; if not, the asset is added to the fund's owned assets,
  reverts all work if any permission, quantity, asset or address are determined to be invalid.


`function redeem() public`

This function determines the quantity of shares owned by `msg.sender` and calls `redeemQuantity()`. A share-commensurate quantity of all token assets in the fund are transferred to `msg.sender`, i.e. the investor.


`function redeemQuantity(uint shareQuantity) public`

This function allows the investor to redeem a specific quantity of shares held by the investor.


`function redeemWithConstraints(uint shareQuantity, address[] requestedAssets) public returns (bool)`

This function:
  ensures that the requested redemption share quantity is less than or equal to the share quantity owned by the investor (`msg.sender`),
  ensures that individual asset tokens are only redeemed once,
  maintains parity between `requestedAssets[]` array and the `redeemedAssets[]` array,
  calculates the proportionate quantity of each token asset owned by the investor (`msg.sender`) based on share quantity,
  destroys the investor's shares,
  safely transfers all token assets in the correct quantities to the investor's address.


Document factory?
