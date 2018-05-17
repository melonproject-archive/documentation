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

## Asset Custody
In the context of a Melon Fund, Assets are cryptographic Tokens [link] adhering to the ERC20 standard [link] on the Ethereum blockchain.  The Asset universe for Melon Funds necessarily requires a that a liquid market exists for the token on one or more decentralized exchanges [link] in order to facilitate price feed and trade execution functionality. The Technical Council [link] shall determine membership of specific Tokens in the Melon Asset universe.

In the legacy financial system, assets are held by custodian banks.

## Structs
The Melon Fund contract specifies the following types as structs:

- Modules - Modules represent non-core fund functionality which are implemented as separate contracts that adhere to a standardized interface.

 - Price Feed - Mandatory module which delivers real-time price information to be used internally by the Melon Fund for valuation and performance calculation purposes, as well as conducting risk management.

 - Compliance - Discretionary module which can specify the Investor interaction with the Melon Fund.

 - Risk - Discretionary module which defines sophisticated and parameterizable *ex ante* rules pertaining to Melon Fund set up and certain pre-trade checks.

  - Discretionary modules are chosen and parameterized at fund set up.


- Calculations - The Calculations struct contains the following members representing specific, internal calculations as required by Melon Fund functionality:

  - GAV - Gross Asset Value - The sum of the asset-weighted price values of the Melon Fund's underlying holdings.

    ![ee](http://www.sciweavers.org/tex2img.php?eq=gav%20%3D%20%20%5Csum_%7Bk%3D1%7D%5En%20assetHolding%28a_%7Bk%7D%29%2AassetPrice%28a_%7Bk%7D%29%20%20&bc=White&fc=Black&im=jpg&fs=12&ff=arev&edit=0)

  - NAV - Net Asset Value - The GAV less liabilities. For a Melon Fund, liabilities will at any point in time be unclaimed Management- and Performance Fees.

  - Management Fee - The fee paid by the Investor to the Investment Manager as compensation for ongoing selection and allocation activities of the underlying Melon Fund holdings. This fee is specified by the Investment Manager at fund set up and is stated as a percentage per annum figure. The fee accrual is asset value- and time-weighted. Management Fees directly take the form of the Melon Fund's shares and are affected as share inflation. A beneficial side-effect of this is the aligned incentive of Investment Manager and the Investor.

  - Performance Fee - The fee paid by the Investor to the Investment Manager as compensation for positive performance over a given performance period. The fee is specified by the Investment Manager at fund set up and is stated as a percentage of positive performance over a given performance period. Performance Fees are calculated based on performance *after* the deduction of realized Management Fees. Performance Fees directly take the form of the Melon Fund's shares and are affected as share inflation. Beneficial side-effects of this are the aligned incentive of Investment Manager and the Investor, and a simpler, more elegant calculation mechanic.

  - Unclaimed Fees - That portion of accrued Management- and Performance Fees due to the Investment Manager, but not claimed. Unclaimed fees represent a liability which reduces the GAV of the Melon Fund. [Note: In subsequent versions, it may be interesting to implement this: As a condition to claiming fees, the Investment Manager must remedy all current, passive Risk Engineering breaches.]

  - High-watermark - Indicates the highest performance level or NAV within a given period. If the NAV at the time of measurement is less than the previous High-watermark, then the previous High-watermark retains its status. In the current version, this is used to determine if positive fund performance has been achieved since the end of the previous performance period.

    [Note:  In subsequent versions, because individual Investors can invest at different and arbitrary times, a blanket treatment of the highwatermark must be reworked so that the performance fee amount can be fairly calculated for each individual investment, depending on the time of subscription.]

  - Total Supply - The number of Melon Fund shares currently outstanding for a specific Melon Fund. As new investment capital enters the Melon Fund in the form of subscriptions, the Melon Fund automatically creates new shares, with the amount created being commensurate with the fund NAV or share price at the time of subscription. As investment capital leaves the Melon Fund in the form of redemptions, the Melon Fund destroys the redeemed shares. This is known as an open-ended fund structure.

  - Timestamp - Time of an event taken from `now`, an alias for `block.timestamp` which retrieves the the timestamp of the highest block. The timestamp is quoted in Unix time format.


- Request - The Request struct contains the following members, describing and logging all Melon Fund interactions pertaining to investment capital entering or leaving the Melon Fund from shareholders/participants.

  - participant - Address of the investor requesting the subscription or redemption.

  - status - Status of request. Possible values are `active`, `cancelled` and `executed`.

  - requestType - Type of the request. Possible values are `invest`, `redeem`, and `tokenFallbackRedeem`.

  - requestAsset - Address of the asset being requested. <span style="color:red">CHECK:</span> This is the Melon Fund base asset in which valuation occurs.

  - shareQuantity - Quantity of Melon Fund shares subject to the subscription or redemption.

  - giveQuantity - Quantity in Melon Fund base asset with which Investor subscribes to Melon Fund, receiving
  shareQuantity shares in exchange.

  - receiveQuantity - Quantity of Melon Fund base asset to be received by Investor from the Melon Fund redemption, giving the shareQuantity shares in exchange.

  - timestamp - Time of request creation in seconds as specified by `block.timestamp` in Unix time format.

  - atUpdateId - Price Feed updateId when this request was created.


- Exchange - The Exchange struct contains the following members describing all required information for the Melon Fund's interaction with a specific exchange.

  - exchange - Address of the DEX smart contract.

  - exchangeAdapter - Address of the adapter contract for a given DEX.

  - takesCustody - Boolean value specifying whether the DEX smart contract takes custody of trading asset before listing the order in the DEX order book.


- Order - The Order struct contains the following members describing all required information for the Melon Fund's interaction with a specific order.

  - id - The order's Id.

  - expiredAt - Timestamp value determining when a specific order expires.

By default, solidity function types are `public`.


## External Functions
These functions define how agents external to the Ethereum Blockchain interact with the Fund.

#### Administration Functions

  - `enableInvestment()` - `Owner` is able to enable subscription with specified assets. When executed, the Melon Fund will allow subscription requests denominated in the assets specified.

  - `disableInvestment)()` - `Owner` is able to disable subscription with specified assets. When executed, the Melon Fund will reject subscription requests denominated in the assets specified.

  - `enableRedemption()` -`Owner` is able to enable redemption in specified assets. When executed, the Melon Fund will allow redemption requests denominated in the assets specified.

  - `disableRedemption()` - `Owner` is able to disable redemption in specified assets. When executed, the Melon Fund will reject redemption requests denominated in the assets specified.

  - `shutDown()` - `Owner` is able to shut the Melon Fund instance down.

#### Participation Functions

  - `requestInvestment()` - Creates a subscription Request specifying the subscription asset, the subscription asset quantity and the quantity of shares to be received. The request is added to the requests array with a status of `Active`.

  - `requestRedemption()` - Creates a redemption Request specifying the redemption asset, the redemption asset quantity and the quantity of shares to be redeemed. The request is added to the requests array with a status of `Active`.

  - `executeRequest()` - Executes a specific `Active` investment or redemption Request on the requests array in a way that minimizes potential information advantages of the Investor.

  - `cancelRequest()` - Cancels any `Active` Request on the requests array.

  - redeemAllOwnedAssets() - Wrapper function for emergencyRedeem() which attempts to redeem *all* assets in a slice. Function operates independently from an active Price Feed.

#### Managing Function

  - `callOnExchange()` - `Owner` is able to invoke a specific function call outside of the fund smart contract to the DEX smart contract.

Functions Owner or Fund:

  - `addOpenMakeOrder()` - `Owner` or Fund itself `address(this)` adds a make order to the DEX'a order book and specifies it's expiration time.

  - `removeOpenMakeOrder()` - `Owner` or Fund itself `address(this)` deletes a make order from the DEX'a order book.

## Public Functions

  - `tokenFallback()` - ERC223 functionality.

  - `addAssetToOwnedAssets()` - Public function where `Owner` or Fund itself `address(this)` can add an asset to the list of assets owned by the Melon Fund.


#### Accounting

  - `calcGav()` - Public function which calculates Gross Asset Value (GAV) of the Melon Fund. GAV is is quoted in fund-specified Quote Asset terms. CHECK: Public?

  - `calcSharePriceAndAllocateFees()` - Public function which calculates unclaimed fees of the manager in share terms and creates new shares adding the quantity to total share supply.

  - `emergencyRedeem()` - Public function which calculates the Investor's pro rata share of each Melon Fund holding and transfers that quantity of each asset to the Investor's address. Also known as a "slice redemption" or redemption-in-kind. Function then destroys the shares submitted for redemption, reducing the Melon Fund's total supply.

#### Fees

  - quantityHeldInCustodyOfExchange() - Function which calculates and returns the quantity of asset held in custody across exchanges.


## View Functions

  - `calcUnclaimedFees()` - View function which calculates and returns the accrued management fee, performance fee and total unclaimed fees due to the manager at the point in time of the function call.

  - `calcNav()` - View function which calculates and returns the Melon Fund's Net Asset Value (NAV). This is the GAV less unclaimed management- and performance fees due.

  - `calcValuePerShare()` - View function which calculates and returns the Melon Fund's share price. CHECK: This is the (GAV - Mfee) / total number of Melon Fund shares.

  - `performCalculations()` - View function which calculates and returns the GAV, NAV, Management fee, performance fee, unclaimed fees, fees in share terms and the share price.

  - `calcSharePrice()` - View function which calculates sharePrice denominated in the Melon Fund's base asset (CHECK: Quote Asset).

  - `getModules()` - View function which returns the addresses of the Price Feed, Compliance and Risk Management module contracts.

  - `getLastRequestId()` - View functions which returns the Id of the last Request submitted.

  - `getManager()` - View function which returns the address of the Investment Manager, i.e. `Owner`.

  - `getOwnedAssetsLength()` - View function which returns the number of assets held by the Melon Fund.

  - `orderExpired()` - View function which returns a boolean indicating whether an order on a given exchange for a given asset has expired.
