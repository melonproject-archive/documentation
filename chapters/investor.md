# Investors

A Melon Fund is setup to serve Investors.  That is, an Investment Manager creates a Melon Fund in order to provide the service of managing capital on behalf of participating Investors. 

Investors are individuals or institutions participating in the performance of Melon Funds by sending cryptographic Asset tokens to the specific Melon Fund via the fund's `requestInvestment()` function.

In the context of the platform, the Investor is the Ethereum address from which the `requestInvestment()` was called and tokens sent (i.e. `msg.sender`).

## Investor Address
The individual Investor must retain the responsibility of secure custody of the Investor address's private key. The private key is required to sign valid Ethereum transactions which are ultimately responsible for calling specific functions and sending token amounts, covering the activities of investing, redeeming and redeeming by slice.

WARNING: Loss or poor security of the Investor address's private key will result in the irreversible and permanent loss of all crypto asset tokens associated with that address.

## Ethereum Addresses and Keys
An Ethereum address takes the hexidecimal format of 20 bytes prefixed by "0x". Ethereum addresses are all lowercase, but may be mixed case if a checksum is implemented. The private key is 256 random bits.

Example Ethereum Address:                
`0xdd4A3d9D44A670a80bAbbd60FD300a4C8C38561f`

Example Ethereum Private Key:
`d43689ae52d6a4e0e95d41bc638e95a66c4e7d0852f6db83d44c234ce9267d0d`


Investing addresses can interact with the Melon Fund through the following functions:

## External Functions: Participation

#### `requestInvestment()`

The only correct entry point into the Melon Fund for tokens sent from the Investor address. Investors are able in invest with ETH or MLN.

Conditions:

- The Melon Fund must not be in the state: `isShutDown == true`.
- The asset in which the investment request is being made is currently permitted: `isInvestAllowed[investmentAsset] == true`
- If Compliance criteria has been set with the Compliance Module by the Investment Manager, a check against these criteria is run to ensure that `isInvestmentPermitted() == true`.


#### `executeRequest()`

This function is called to process an active request.  The function enforces conditions and delays such that no party can materially exploit asymmetric informational advantages inherent in transparent decentralized exchanges.

Conditions for successful request execution:

- The Melon Fund must not be in the state: `isShutDown == true`.

- The request's status must be active: `requests[id].status == RequestStatus.active`.

- The request's type must not be a redemption, or if it is, the quantity of shares to be redeemed must be less than or equal to the Investor's shares held: `requests[id].requestType != RequestType.redeem || requests[id].shareQuantity <= balances[requests[id].participant]`.

- The total quantity of shares in the Melon Fund is 0 (i.e. first investment) `totalSupply == 0` or, at least one price feed update time interval (in seconds) *and* two price feed updates have occurred.

#### `cancelRequest()`

Investors may cancel active investment/redemption requests by calling this function.

Conditions for successful request cancellation:

- The request's status must be active: `requests[id].status == RequestStatus.active`

- The caller is the request's original issuer or the fund is shut down: `requests[id].participant == msg.sender || isShutDown`

#### `redeemAllOwnedAssets()`

Investors may redeem their pro-rata share of the Melon Fund's underling individual positions. Investors will receive each individual token in the proportionate quantity. This is also called "redemption by Slice" or "redemption in kind". This action can also be used when the fund has been shut down, liquidity is low or when the underlying tokens themselves are desired.

Condition for successful Slice redemption:

- The caller must own a quantity of Melon Fund shares greater or equal to the quantity requested for redemption: `balances[msg.sender] >= shareQuantity`

## Notes
NOTE: Investors are responsible for having sufficient quantities of ETH in their Investor address to successfully call invest or redeem functions!

NOTE: Sending tokens directly to the address of the fund will cause spurious results in the calculation of the NAV and should be avoided.
