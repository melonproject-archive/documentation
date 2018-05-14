---
layout: page
title: Price Feed
---

# Price Feed

In true decentralized fashion, the ability to provide services to the Melon Protocol platform is permissionless and open to parties willing and capable to reliably provide such services.

However, precisely because such services are permissionless, they must be subject to review by the platform community and stakeholders. These parties must have tools at their disposal to take corrective action should malicious or negligent behavior be detected.

Data which is exogenous to the blockchain must be actively provided by a third party. Asset price data is critical to fund management and Melon Funds. Current and accurate price data enables real-time valuation and indispensible context for investment management decisions.  As such, careful attention must be taken to *how* this exogenous data enters the Melon Fund platform.

Independent parties wishing to provide the service of an asset price feed may do so by running software which gathers asset price data (corresponding to the Melon Asseet Universe set forth in the Melon Asset Registrar) and reliably feeding this data in a specifed format to the Melon Platform.

#### Operator Staking

Prospective Price Feed Operators (candidates) must offer a deposit or "stake" in Melon Tokens (MLN). Individual Price Feed Operators are then "selected" by the Melon Platform solely on the level of their stake. The staked MLN is held securely in custody by a smart contract which also determines which of the candidates is actually feeding price data to the Melon Fund Platform.

Price Feed Operator selection occurs solely on the basis of the relative amout of MLN staked, with the highest staking candidates awarded the ability to submit price data.

As previously stated, high-qualtiy and reliable price data is crucial to the operation of the Melon Fund Platform. To mitigate the risk that corrupt or irregular data (be the data malicious or negligent) enters the Melon Fund Platform's context, price feed operation has been designed to sample price data across multiple price feed operators. The Melon Protocol specifies a minimum- and maximum number of Price Feed Operators. In this way, candidate Price Feed Operators can "self-select" by staking the requisite amount of MLN Tokens which ranks them among the top *n* MLN stakers as specifed by the maximum number of Price Feed Operators. This ranking may change at each block depending on how each Price Feed Operator changes their staked amount of MLN Tokens.

It should be explicitly stated that once a candidate ranks as a Price Feed Operator, the magnitude of their amount has no impact on whether their price data point is selected to cannonically represent the specific asset's market price at that point in time. That is to say, Price Feed Operators cannot influence the selection of the price data point, but only their ability to *be* an active Price Feed Operator.


The sampling of the price feed data is implemented as the statistical median for a given asset's price feed update. The median observation for a specific asset's market price for a specific point in time is selected by the Melon Protocol to cannonically represent that price point. Should the number of active Price Feed Operators be even at the time of a given Price Feed Update, the standard practice of taking the arithmetic average of the middle two ranked observations applies.


#### Price Feed Update

The Price Feed Update establishes, on the blockchain, the performance track record of individual assets over time. Once selected by the Melon Protocol and written to state, the price point is a permanent part of the historical track record of the asset. The frequency of the Price Feed Update is defined by the Price Feed Interval.


#### Price Feed Interval

The Price Feed Interval is the time interval or period defined by the Melon Protocol between two Price Feed Updates.


#### Current Governance

Staking value only has effective economic incentive if the value can be lost. The current iteration of the Melon Protocol provides a mechanism to penalize staking Price Feed Operators for perceived misconduct or negligence. The mechanism allows the Melonport AG multi-signature wallet to specifically and discretionarily "burn" staked MLN Tokens, effectively appropriating and removing the offending Price Operator's stake, in a provable way, from the MLN Token supply.

#### Future Governance

It is the intention provide a more decentralized governance mechanism which can be applied to different domains of the Melon Protocol, including Price Feed Operator Governance. This envisions the creation of a "Technical Council" to adjudicate. For further details on the Technical Council, please refer to the Governance section.

#### Public Functions

`update()`  only owner
Function callable only by contract `owner` which updates price of asset(s) relative to the quote asset. An array of asset addresses and an equal length array of asset prices are sent as parameters to the function call.

#### Public View Functions

`getQuoteAsset()`
Function which returns the address of the quote asset.

`getInterval()`
Function which returns the frequency of asset price updates in seconds.

`getValidity()`
Function which returns the time in seconds for which price data is considered current; the `VALIDITY` parameter.

`getLastUpdateId()`
Function which returns the Id of the last update. Can be viewed as an update counter.

`hasRecentPrice()`
Function which returns a boolean indicating whether a specific asset has had a current price update. Current is determined by the number of seconds specified by the `VALIDITY` parameter.

`hasRecentPrices()`
Function which returns a boolean indicating whether an array of assets has had a current prices update. Current is determined by the number of seconds specified by the `VALIDITY` parameter.

`getPrice()`
Function which returns the validity, price and decimals of a specific asset.

`getPrices()`
Function which returns the validity, price and decimals of an array of assets.

`getInvertedPrice()`
Function which returns the validity, inverted price and number of decimals of the quote asset.

`getReferencePrice()`
Function which returns the validity, reference price and number of decimals for the asset given the address of the base asset and the address of the quote asset.

`getOrderPrice()`
Function which returns the implicit price of an order given the sell asset address, buy asset address, sell quantity and buy quantity. CHECK: buyAsset not used.

`existsPriceOnAssetPair()`
Function which returns a boolean indicating whether current price data for a specific buy/sell asset pair exists. One of the specified assets must be the quote asset.
