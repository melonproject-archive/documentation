# Shares

### Shares.sol


### General


#### Description

In fund management, shares assign proportionate ownership of the collective underlying assets held in the fund. Each share in a fund has equal claim to the fund's assets. Shareholders then have a claim to the underlying fund assets in proportion to the quantity of shares under their control, i.e. ownership.

In a Melon fund, fund shares are represented as ERC20 tokens. Each token has equal and proportionate claim to the underlying assets. The share tokens are fungible but are not transferrable [CHECK if this will remain so.] between owners.

As subscribed capital enters the fund, the Shares contract will create a proportionate quantity of share tokens based on the current market prices of the fund's underlying assets and the market price of the subscribed capital and assign allocate these newly created share tokens to the subscribing address.

When share tokens are redeemed. A proportionate quantity of the fund's underlying asset tokens are transferred to the redeeming address and the redeeming address's redeemed share tokens are destroyed (i.e. set to 0) and the fund's total share quantity is reduced by that same quantity.

#### Inherits from
Spoke, StandardToken (links)

#### On construction

The Shares contract requires the address of the `hub` and becomes a `spoke` of the `hub`. The Shares contract describes the Melon fund's shares as *ERC20 tokens*. The share tokens take their name as defined by the `hub`. The share token's symbol and decimals are hardcoded in the contract.

The Shares contract is created from the sharesFactory contract, which creates a new instance of `Shares` given the `hub` address, register the address of the newly created shares contract as a child of the sharesFactory.


#### Public State variables

`string public symbol`

A public string variable denoting the Melon fund's share token symbol, currently defaulting to "MLNF".

`string public name`

A public string variable denoting the Melon fund's name as specified by the manager at set up.

`uint8 public decimals`

A public integer variable storing the decimal precision of the Melon fund's share quantity, currently defaulting to 18, the same decimal precision as ETH and many other ERC20 tokens.


#### Structs

None.

#### Enums

None.

#### Public Functions

`function createFor(address who, uint amount) auth`

This function requires that the caller is the `owner` or the current contract. This function calls internal function `_mint()`, which increases both `totalSupply` and the `balance` of the address by the same quantity.

`function destroyFor(address who, uint amount) auth`

This function requires that the caller is the `owner` or the current contract. This function calls internal function `_burn()`, which decreases both `totalSupply` and the `balance` of the address by the same quantity.

#### TBD Reverting functions:

`function transfer(address to, uint amount) public returns (bool)`


`function transferFrom(address from, address to, uint amount) public returns (bool)`


`function approve(address spender, uint amount) public returns (bool)`


`function increaseApproval(address spender, uint amount) public returns (bool)`


`function decreaseApproval(address spender, uint amount) public returns (bool)`
