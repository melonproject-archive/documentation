---
layout: page
title: Fund
---
# Fund

The core functionality of the Melon Protocol is captured by the concept of a "fund". This functionality is specified in fund.sol.

The Melon Fund is responsible for, and performs:
- Asset Custody
- Performance Accounting
- Fee Accounting
- Shareholder Accounting
- Exchange Integration
- Market Price Data Feed Integration
- Trade Execution

At fund instantiation, the fund creator (Investment Manager, "owner") may discretionarily elect to activate Compliance and/or Risk Engineering functionality. This enables the fund to constrain Investor or Investment Manager interaction in specific and configurable ways.

#### Asset Custody
In the context of a Melon Fund, Assets are cryptographic Tokens [link] adhering to the ERC20 standard [link] on the Ethereum blockchain.  The Asset universe for Melon Funds necessarily requires a that a liquid market exists for the token on one or more decentralized exchanges [link] in order to facilitate price feed and trade execution functionality. The Technical Council [link] shall determine membership of specific Tokens in the Melon Asset universe.

In the legacy financial system, assets are held by custodian banks.

#### Structs
The Melon Fund contract specifies the following types as structs:

- Modules - Modules represent non-core fund functionality which implemented as separate contracts which adhere to a standardized interface.

 - Price Feed - Mandatory module which delivers real-time price information to be used internally by the Melon Fund for valuation- and performance calculation purposes.

 - Compliance - Discretionary module which can specify the Investor interaction with the Melon Fund.

 - Risk - Discretionary module which defines sophisticated and parameterizable *ex ante* rules pertaining to Melon Fund set up and certain pre-trade checks.

  - Discretionary modules are chosen and parameterized at fund set up.


- Calculations
  - GAV - Gross Asset Value - The sum of the asset-weighted price values of the Melon Fund's underlying holdings.

    ![ee](http://www.sciweavers.org/tex2img.php?eq=gav%20%3D%20%20%5Csum_%7Bk%3D1%7D%5En%20assetHolding%28a_%7Bk%7D%29%2AassetPrice%28a_%7Bk%7D%29%20%20&bc=White&fc=Black&im=jpg&fs=12&ff=arev&edit=0)

  - NAV - Net Asset Value - The GAV less liabilities. For a Melon Fund, liabilities will at any point in time be unclaimed Management- and Performance Fees.

  - Management Fee - The fee paid by the Investor to the Investment Manager as compensation for ongoing selection and allocation activities of the underlying Melon Fund holdings. This fee is specified by the Investment Manager at fund set up and is stated as a percentage per annum figure. The fee accrual is asset value- and time-weighted. Management Fees directly take the form of the Melon Fund's shares and are affected as share inflation. A beneficial side-effect of this is the aligned incentive of Investment Manager and the Investor.

  - Performance Fee - The fee paid by the Investor to the Investment Manager as compensation for positive performance over a given performance period. The fee is specified by the Investment Manager at fund set up and is stated as a percentage of positive performance over a given performance period. Performance Fees are calculated based on performance *after* the deduction of realized Management Fees. Performance Fees directly take the form of the Melon Fund's shares and are affected as share inflation. Beneficial side-effects of this are the aligned incentive of Investment Manager and the Investor, and a simpler, more elegant calculation mechanic.

  - Unclaimed Fees - That portion of accrued Management- and Performance Fees due to the Investment Manager, but not claimed. Unclaimed fees represent a liability which reduces the GAV of the Melon Fund. [Note: In subsequent versions, it may be interesting to implement this: As a condition to claiming fees, the Investment Manager must remedy all current, passive Risk Engineering breaches.]

  - High-watermark - Indicates the highest performance level or NAV within a given period. If the NAV at the time of measurement is less than the previous High-watermark, then the previous High-watermark retains its status. In the current version, this is used to determine if positive fund performance has been achieved since the close previous performance period.

    [Note:  In subsequent versions, because individual Investors can invest at different and arbitrary times, a blanket treatment of the highwatermark must be reworked so that the performance fee amount can be fairly calculated for each individual investment, depending on the time of subscription.]
  - Total Supply - This is the number of Melon Fund shares currently outstanding.

  - Timestamp - Time of an event taken from `now`, an alias for `block.timestamp` which retrieves the the timestamp of the highest block.



- Request - Describes and logs whenever asset enter and leave fund due to Participants.
  - participant
  - status
  - requestType

- Exchange
- Order

By default, solidity function types are `internal`


Functions External:

enableInvestment()
disableInvestment)()
enableRedemption()
disableRedemption()
shutDown()
requestInvestment()
requestRedemption()
executeRequest()
cancelRequest()
redeemAllOwnedAssets()
callOnExchange()

Functions Owner or Fund:

addOpenMakeOrder()
removeOpenMakeOrder()
tokenFallback()
addAssetToOwnedAssets()


Functions Public:

calcGav()
calcSharePriceAndAllocateFees()
emergencyRedeem()
quantityHeldInCustodyOfExchange()


Functions View:

calcUnclaimedFees()
calcNav()
calcValuePerShare()
performCalculations()
calcSharePrice()
getModules()
getLastRequestId()
getManager()
getOwnedAssetsLength()
orderExpired()
