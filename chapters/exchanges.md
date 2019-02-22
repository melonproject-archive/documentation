# Exchange Adapters

## General

Exchanges are each integrated into the water<b>melon</b> protocol through exchange adapter contracts. Exchange adapter contracts each inherit from the standard contract ExchangeAdapter.sol. Each adapter then maps this functionality to the specific exchange contract's defined functionality, overriding the base contract's public methods.
&nbsp;

## ExchangeAdapter.sol

#### Description

The ExchangeAdapter contract intended to generalize the implementation of any concrete exchange adapter.

The function parameter signatures for the functions `makeOrder()`, `takeOrder()` and `cancelOrder()` are identical. The parameters are summarized here:

`address targetExchange` - The address of the intended exchange contract pertaining to the order.

`address[6] orderAddresses` - An array containing address pertaining as per below to the order.
  `orderAddresses[0]` - The address of the order maker.
  `orderAddresses[1]` - The address of the order taker.
  `orderAddresses[2]` - The address of the maker asset token contract.
  `orderAddresses[3]` - The address of the taker asset token contract.
  `orderAddresses[4]` - The address of the fee recipient, `feeRecipientAddress`.
  `orderAddresses[5]` - The address of the sender, `msg.sender`.

`uint[8] orderValues` - An array containing values pertaining as per below to the order.
  `orderValues[0]` - The maker asset quantity.
  `orderValues[1]` - The taker asset quantity.
  `orderValues[2]` - The maker fee.
  `orderValues[3]` - The taker fee.
  `orderValues[4]` - The expiration time of the order in seconds.
  `orderValues[5]` - The salt/nonce used to differentiate orders of like assets and quantities.
  `orderValues[6]` - The order fill quantity, i.e. the quantity of the taker asset token traded.
  `orderValues[7]` - The signature mode specific to the Dexy exchange.

`bytes32 identifier` - The order identifier.

`bytes makerAssetData` - Encoded data specific to the maker asset.

`bytes takerAssetData` - Encoded data specific to the taker asset.

`bytes signature` - The signature of the order maker.
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

None.

&nbsp;

#### Modifiers

`modifier onlyManager()`

This function modifier requires that `msg.sender` is the fund manager address prior to executing functionality.
&nbsp;

`modifier notShutDown()`

This function modifier requires that the fund is not in a shut down state prior to executing functionality.
&nbsp;

`modifier onlyCancelPermitted(address exchange, address asset)`

This function modifier requires that either `msg.sender` is the fund manager address, the fund is in a shut down state or the order has expired prior to executing functionality.
&nbsp;

#### Events

None.

&nbsp;

#### Public State Variables

None.

&nbsp;

#### Functions

`function getTrading() internal view returns (Trading)`

This internal view function returns the current contracts address as a `Trading` contract.
&nbsp;

`function getHub() internal view returns (Hub)`

This internal view function returns the `Hub` contract corresponding to the `Trading` contract.
&nbsp;

`function getAccounting() internal view returns (Accounting)`

This internal view function returns the `Accounting` contract corresponding to the current `Hub` contract.
&nbsp;

`function hubShutDown() internal view returns (bool)`

This internal view function returns a boolean indicating the current shut down state of the fund.
&nbsp;

`function getManager() internal view returns (address)`

This internal view function returns the address of the current fund's fund manager.
&nbsp;

`function ensureNotInOpenMakeOrder(address _asset) internal view`

This internal view function ensures that no open make order currently exists for the asset token address provided.
&nbsp;

`function makeOrder(
    address targetExchange,
    address[6] orderAddresses,
    uint[8] orderValues,
    bytes32 identifier,
    bytes makerAssetData,
    bytes takerAssetData,
    bytes signature)`

This public function must ensure that:

  the fund is not shut down,
  the asset token prices are recent,
  applicable risk policies are evaluated,
  asset tokens to be traded are approved (if required),
  the make order transaction is sent to the exchange contract,
  the order is registered on the exchange contract (if possible),
  the acquired asset token, if not already held, is added to `ownedAssets`.

The function creates a make order on a specific exchange specifying the asset tokens to exchange and the corresponding quantities (implicit price) required for the order. The function on the base contract remains unimplemented and reverts.
&nbsp;

`function takeOrder(
    address targetExchange,
    address[6] orderAddresses,
    uint[8] orderValues,
    bytes32 identifier,
    bytes makerAssetData,
    bytes takerAssetData,
    bytes signature)`

This public function must ensure that:

  the fund is not shut down,
  the fund is not filling its own make order,
  the asset token price pair exists in the price source,
  the asset token prices are recent,
  applicable risk policies are evaluated,
  asset tokens to be traded are approved (if required),
  the take order transaction is sent to the exchange contract,
  the order is removed from the exchange contract (if possible),
  the acquired asset token, if not already held, is added to `ownedAssets`.

The function takes the opposite side of an existing make order on a specific exchange, delivering the asset token demanded by the make order in exchange for the asset token supplied by the make order, in the quantities (or less for partial order fills) specified.  The function on the base contract remains unimplemented and reverts.
&nbsp;

`function cancelOrder(
    address targetExchange,
    address[6] orderAddresses,
    uint[8] orderValues,
    bytes32 identifier,
    bytes makerAssetData,
    bytes takerAssetData,
    bytes signature)`

This function must ensure that:

    the `msg.sender` is the order owner or the order has expired or the fund is shut down,
    the order is removed from the fund-internal order-tracking array,
    the order on the exchange is canceled.

The function cancels and removes all order information within the fund and on the exchange where it existed. The function on the base contract remains unimplemented and reverts.
&nbsp;

`function getOrder(
    address onExchange,
    uint id,
    address makerAsset)
view returns (
    address,
    address,
    uint,
    uint)`

This public view function is meant to return the relevant order information (maker asset token address, taker asset token address, maker asset token quantity and taker asset token quantity) given the exchange contract address, the order identifier and the maker asset token contract address parameters. The function on the base contract remains unimplemented and reverts.
&nbsp;

## EngineAdapter.sol

#### Description

This contract is the exchange adapter to the water<b>melon</b> Engine and serves as the interface from a water<b>melon</b> fund to the water<b>melon</b> Engine for the express purposes of the water<b>melon</b> fund trading MLN to the water<b>melon</b> Engine in exchange for WETH.
&nbsp;

#### Inherits from

DSMath, TokenUser, ExchangeAdapter (link)

&nbsp;

#### On Construction

None.

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

`function takeOrder (
    address targetExchange,
    address[6] orderAddresses,
    uint[8] orderValues,
    bytes32 identifier,
    bytes makerAssetData,
    bytes takerAssetData,
    bytes signature)
    onlyManager notShutDown`

The following parameters are used by the function:

`targetExchange` - The address of the water<b>melon</b> Engine exchange contract.
`orderAddresses[0]` - The address of the MLN token contract (maker asset token).
`orderAddresses[1]` - The address of the WETH token contract (taker asset token).
`orderValues[0]` - The quantity of the MLN token, expressed in 18 decimal precision.

This function requires that the `msg.sender` is the fund manager and that the fund is not shut down. The function then requires that desired MLN token trade quantity be approved by the water<b>melon</b> fund. The function calls the water<b>melon</b> Engine to get the corresponding quantity of ETH. The water<b>melon</b> Engine function `sellAndBurnMln` is called, as the fund transfers MLN for WETH, as the WETH is received by the water<b>melon</b> fund and transferred to the water<b>melon</b> fund's vault, `ownedAssets` is updated with the new position if required and finally, the order status is updated.
&nbsp;

## ZeroExV2Adapter.sol

#### Description

This contract is the exchange adapter to the 0x v2 Exchange contract and serves as the interface from a water<b>melon</b> fund to the 0x v2 Exchange for purposes of exchange of asset tokens listed on the 0x v2 Exchange.
&nbsp;

#### Inherits from

ExchangeAdapter, DSMath (link)

&nbsp;

#### On Construction

None.

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

`function makeOrder(
    address targetExchange,
    address[6] orderAddresses,
    uint[8] orderValues,
    bytes32 identifier,
    bytes wrappedMakerAssetData,
    bytes takerAssetData,
    bytes signature
) onlyManager notShutDown`

Please see parameter descriptions above.

This public function requires that the `msg.sender` is the fund manager and that the fund is not shut down. The function creates a make order on the 0x v2 exchange contract. It ensures that the maker asset token is not currently listed in any other open make order the water<b>melon</b> fund may have on any exchange. The taker asset token is preliminarily added to the water<b>melon</b> fund's owned assets and an open make order is added to the water<b>melon</b> fund's internal order tracking. Finally, the order is pre-signed on the 0x v2 exchange contract authorizing manager's signature on behalf of the trading contract.
&nbsp;

`function cancelOrder(
    address targetExchange,
    address[6] orderAddresses,
    uint[8] orderValues,
    bytes32 identifier,
    bytes wrappedMakerAssetData,
    bytes takerAssetData,
    bytes signature
) onlyCancelPermitted(targetExchange, orderAddresses[2])`

Please see parameter descriptions above.

This public function cancels an existing order on the 0x v2 exchange contract by calling the 0x v2 exchange contract's `cancelOrder()` function. The function applies the `onlyCancelPermitted` modifier, allowing the cancel to only be submitted under one of these conditions: the fund manager cancels the order, the fund is shut down or the order has expired. The asset token is finally removed from the water<b>melon</b> fund's internal order tracking.
&nbsp;

`function getOrder(
    address onExchange,
    uint id,
    address makerAsset)
view returns (
    address,
    address,
    uint,
    uint)`

This public view function returns the relevant order information (maker asset token address, taker asset token address, maker asset token quantity and taker asset token quantity) given the exchange contract address, the order identifier and the maker asset token contract address parameters. The function can determine whether the order has been partially or fully filled (returning the remaining maker asset token quantity and the remaining taker asset token quantity)
&nbsp;

`function approveTakerAsset(
    address targetExchange,
    address takerAsset,
    bytes takerAssetData,
    uint fillTakerQuantity)
internal`

This internal view function withdraws fillTakerQuantity amount of the taker asset from the vault and approves the same amount to the asset proxy
&nbsp;

`function approveMakerAsset(
    address targetExchange,
    address makerAsset,
    bytes makerAssetData,
    uint makerQuantity)
internal`

This internal view function withdraws makerQuantity amount of the maker asset from the vault and approves the same amount amount to the asset proxy
&nbsp;

`function constructOrderStruct(
    address[6] orderAddresses,
    uint[8] orderValues,
    bytes makerAssetData,
    bytes takerAssetData)
internal
view
    returns (LibOrder.Order memory order)`

This internal view function returns a populated `order` struct based on the parameter values provided.
&nbsp;

`function getAssetProxy(
    address targetExchange,
    bytes assetData)
internal
view
    returns (address assetProxy)`

This internal view function returns the address of the asset proxy given the address of the Ethfinex exchange contract and asset data provided.
&nbsp;

`function getAssetAddress(
    bytes assetData)
internal
view
    returns (address assetAddress)`

This internal view function returns the address of the asset given the asset data provided.
&nbsp;

## EthfinexAdapter.sol

#### Description

This contract is the exchange adapter to the Ethfinex Exchange contract and serves as the interface from a water<b>melon</b> fund to the Ethfinex Exchange for purposes of exchange of asset tokens listed on the Ethfinex Exchange.
&nbsp;

#### Inherits from

ExchangeAdapter, DSMath, DBC (link)

&nbsp;

#### On Construction

None.

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

`function makeOrder(
    address targetExchange,
    address[6] orderAddresses,
    uint[8] orderValues,
    bytes32 identifier,
    bytes wrappedMakerAssetData,
    bytes takerAssetData,
    bytes signature
) onlyManager notShutDown`

Please see parameter descriptions above.

This public function requires that the `msg.sender` is the fund manager and that the fund is not shut down. The function creates a make order on the Ethfinex exchange contract. It ensures that the maker asset token is not currently listed in any other open make order the water<b>melon</b> fund may have on any exchange. The function signs the order and ensures the signature is valid. The taker asset token is preliminarily added to the water<b>melon</b> fund's owned assets and an open make order is added to the water<b>melon</b> fund's internal order tracking. Finally, the order is added to the Ethfinex exchange contract.
&nbsp;

`function cancelOrder(
    address targetExchange,
    address[6] orderAddresses,
    uint[8] orderValues,
    bytes32 identifier,
    bytes wrappedMakerAssetData,
    bytes takerAssetData,
    bytes signature
) onlyCancelPermitted(targetExchange, orderAddresses[2])`

Please see parameter descriptions above.

This public function cancels an existing order on the Ethfinex exchange contract by calling the Ethfinex exchange contract's `cancelOrder()` function. The function applies the `onlyCancelPermitted` modifier, allowing the cancel to only be submitted under one of these conditions: the fund manager cancels the order, the fund is shut down or the order has expired. The asset token is finally removed from the water<b>melon</b> fund's internal order tracking.
&nbsp;

`function withdrawTokens(
    address targetExchange,
    address[6] orderAddresses,
    uint[8] orderValues,
    bytes32 identifier,
    bytes makerAssetData,
    bytes takerAssetData,
    bytes signature)`

Please see parameter descriptions above.

This public function withdraws all asset tokens held by the Ethfinex exchange in open make orders, then removes the make order form the water<b>melon</b> fund's internal order tracking by calling the Trading contract's `removeOpenMakeOrder()`. Finally, the function returns the asset tokens to the vault and adds the asset token to `ownedAssets`.
&nbsp;

`function getOrder(
    address onExchange,
    uint id,
    address makerAsset)
view returns (
    address,
    address,
    uint,
    uint)`

This public view function returns the relevant order information (maker asset token address, taker asset token address, maker asset token quantity and taker asset token quantity) given the exchange contract address, the order identifier and the maker asset token contract address parameters. The function can determine whether the order has been partially or fully filled (returning the remaining maker asset token quantity and the remaining taker asset token quantity)
&nbsp;


`function wrapMakerAsset(
  address targetExchange,
  address makerAsset,
  bytes wrappedMakerAssetData,
  uint makerQuantity,
  uint orderExpirationTime)
internal`

This internal function withdraws the maker asset token from the water<b>melon</b> fund's vault, wrapping the maker token according to Ethfinex's wrapper registry.
&nbsp;        

`function constructOrderStruct(
    address[6] orderAddresses,
    uint[8] orderValues,
    bytes makerAssetData,
    bytes takerAssetData)
internal
view
    returns (LibOrder.Order memory order)`

This internal view function returns a populated `order` struct based on the parameter values provided.
&nbsp;

`function getAssetProxy(
    address targetExchange,
    bytes assetData)
internal
view
    returns (address assetProxy)`

This internal view function returns the address of the asset proxy given the address of the Ethfinex exchange contract and asset data provided.
&nbsp;

`function getAssetAddress(
    bytes assetData)
internal
view
    returns (address assetAddress)`

This internal view function returns the address of the asset given the asset data provided.
&nbsp;

`function getWrapperToken(
    address token)
internal
view
    returns (address wrapperToken)`

This internal view function returns the address of the Ethfinex-specified wrapped asset token contract given the address of asset token contract provided.
&nbsp;

## KyberAdapter.sol

#### Description

This contract is the exchange adapter to the Kyber Network contract and serves as the interface from a water<b>melon</b> fund to the Kyber Network contract for purposes of exchange of asset tokens listed on the Kyber Network.
&nbsp;

#### Inherits from

ExchangeAdapter, DSMath, DBC (link)

&nbsp;

#### On Construction

None.

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

`address public constant ETH_TOKEN_ADDRESS = 0x00eeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeee`

This public constant state variable represents the ETH token address.
&nbsp;

#### Public Functions

`function takeOrder(
    address targetExchange,
    address[6] orderAddresses,
    uint[8] orderValues,
    bytes32 identifier,
    bytes makerAssetData,
    bytes takerAssetData,
    bytes signature)
onlyManager notShutDown`

Please see parameter descriptions above. In the context of the Kyber exchange contract's nomenclature (swapToken() function), the following parameters take the specified mappings:

  `orderAddresses[2]` - The Src token address.
  `orderAddresses[3]` - The Dest token address.
  `orderValues[0]` - The Src token quantity.
  `orderValues[1]` - The Dest token quantity.

This public function must ensure that:

  the fund is not shut down,
  the `msg.sender` is the fund manager.
  take fill quantity is equal to the taker asset quantity,
  the asset token prices are recent and calculate the minimum required exchange rate,
  applicable risk policies are evaluated,
  asset tokens to be traded are approved (if required),
  the swap order on the Kyber exchange contract is performed,
  the acquired asset token, if not already held, is added to `ownedAssets`. Finally, the acquired asset tokens are returned to the water<b>melon</b> fund's vault and the order status is updated.

Finally, the function returns acquired token assets to the fund's vault and updates the fund's internal order tracking.
&nbsp;

`function dispatchSwap(
    address targetExchange,
    address srcToken,
    uint srcAmount,
    address destToken,
    uint minRate)
internal
    returns (uint actualReceiveAmount)`

This internal function routes the call logic depending on the asset tokens involved. The three possibilities are:

  Fund trades ETH for other asset token - call made to `swapNativeAssetToToken()`
  Fund trades other asset token for ETH - call made to `swapTokenToNativeAsset()`
  Fund trades non-ETH tokens - call made to `swapTokenToToken()`

The function takes the following parameters:

`targetExchange` - The address of the intended exchange contract (i.e. the Kyber Network).
`srcToken` - The address of the asset token the fund will deliver.
`srcAmount` - The quantity of delivering asset token.
`destToken` - The address of the asset token the fund will receive.
`minRate` - The minimum acceptable quantity of the receiving asset token.

The function returns the quantity of the asset token to be received by the fund.
&nbsp;

`function swapNativeAssetToToken(
    address targetExchange,
    address nativeAsset,
    uint srcAmount,
    address destToken,
    uint minRate)
internal
    returns (uint receivedAmount)`

This internal function initiates the trade where the water<b>melon</b> fund delivers ETH for the receiving asset token specified by the `destToken` address parameter. The function withdraws the specified quantity of ETH from the fund's vault sends the ETH quantity to the Kyber Network exchange contract calling its `swapEtherToToken()` function.

The function takes the following parameters:

`targetExchange` - The address of the intended exchange contract (i.e. the Kyber Network).
`nativeAsset` - The address of the native asset token the fund will deliver, ETH.
`srcAmount` - The quantity of delivering asset token.
`destToken` - The address of the asset token the fund will receive.
`minRate` - The minimum acceptable quantity of the receiving asset token. If `minRate` is not defined, the expected rate form the Kyber Network is used.

The function returns the quantity of the `destToken` received. The Kyber Network contract function transfers the asset to this (adapter) contract, where it is then transferred back to the vault in the `takeOrder` function.
&nbsp;

`function swapTokenToNativeAsset(
    address targetExchange,
    address srcToken,
    uint srcAmount,
    address nativeAsset,
    uint minRate)
internal
    returns (uint receivedAmount)`

This internal function initiates the trade where the water<b>melon</b> fund delivers the asset token specified by the `srcToken` address parameter for receiving ETH. The function withdraws the specified quantity of the delivery asset token from the fund's vault and sends the quantity to the Kyber Network exchange contract calling its `swapTokenToEther()` function. The function then approves the transfer quantity for the exchange. Finally, the function converts any ETH received to WETH.

The function takes the following parameters:

  `targetExchange` - The address of the intended exchange contract (i.e. the Kyber Network).
  `srcToken` - The address of the asset token the fund will deliver.
  `srcAmount` - The quantity of delivering asset token.
  `nativeAsset` - The address of the native asset token the fund will receive, ETH.
  `minRate` - The minimum acceptable quantity of the receiving asset token.

The function returns the quantity of the `nativeAsset` received, ETH. The Kyber Network contract function transfers the asset to this (adapter) contract, where it is then transferred back to the vault in the `takeOrder` function.
&nbsp;

`function swapTokenToToken(
    address targetExchange,
    address srcToken,
    uint srcAmount,
    address destToken,
    uint minRate)
internal
    returns (uint receivedAmount)`

This internal function initiates the trade where the water<b>melon</b> fund delivers the asset token specified by the `srcToken` address parameter for receiving the asset token specified by the `destToken` address parameter. The function withdraws the specified quantity of the delivery asset token from the fund's vault and sends the quantity to the Kyber Network exchange contract calling its `swapTokenToToken()` function. The function then approves the transfer quantity for the exchange. Finally, the function converts any ETH received to WETH.

The function takes the following parameters:

  `targetExchange` - The address of the intended exchange contract (i.e. the Kyber Network).
  `srcToken` - The address of the asset token the fund will deliver.
  `srcAmount` - The quantity of delivering asset token.
  `destToken` - The address of the asset token the fund will receive.
  `minRate` - The minimum acceptable quantity of the receiving asset token (`destToken`). If `minRate` is not defined, the expected rate form the Kyber Network is used.

The function returns the quantity of the `destToken` received. The Kyber Network contract function transfers the asset to this (adapter) contract, where it is then transferred back to the vault in the `takeOrder` function.
&nbsp;

`function calcMinRate(
    address srcToken,
    address destToken,
    uint srcAmount,
    uint destAmount)
internal
view
    returns (uint minRate)`

This internal view function returns the minimum acceptable exchange quantity for the taker asset token from the price feed given the following parameters:

`srcToken` - The address of the asset token the fund will deliver.
`destToken` - The address of the asset token the fund will receive.
`srcAmount` - The quantity of delivering asset token.
`destAmount` - The quantity of receiving asset token.
&nbsp;

## MatchingMarketAdapter.sol

#### Description

This contract is the exchange adapter to the OasisDex Matching Market exchange contract and serves as the interface from a water<b>melon</b> fund to the OasisDex Matching Market for purposes of exchange of asset tokens listed on the OasisDex Matching Market.
&nbsp;

#### Inherits from

ExchangeAdapter, DSMath (link)

&nbsp;

#### On Construction

None.

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

`event OrderCreated(uint id)`

This event is emitted when a make order is created on the exchange contract. The event logs and broadcasts the order's identifier.

&nbsp;

#### Public State Variables

None.

&nbsp;

#### Public Functions

`function makeOrder(
    address targetExchange,
    address[6] orderAddresses,
    uint[8] orderValues,
    bytes32 identifier,
    bytes makerAssetData,
    bytes takerAssetData,
    bytes signature)
onlyManager notShutDown`

Please see parameter descriptions above.

This public function must ensure that:

  the fund is not shut down,
  the `msg.sender` is the fund manager.
  the asset token prices are recent,
  applicable risk policies are evaluated,
  asset tokens to be traded are approved (if required),
  the make order transaction is sent to the exchange contract,
  the order is registered on the exchange contract (if possible),
  the acquired asset token, if not already held, is added to `ownedAssets`.

The function creates a make order on the OasisDex Matching Market exchange contract specifying the asset tokens to exchange and the corresponding quantities (implicit price) required for the order.
An `orderID > 0` signifies that the order was successfully submitted to the OasisDex Matching Market exchange contract and the open make order is added to the water<b>melon</b> fund's internal order tracking. Finally, the function emits the `OrderCreated()` with the order's `orderID`.
&nbsp;

`function takeOrder(
    address targetExchange,
    address[6] orderAddresses,
    uint[8] orderValues,
    bytes32 identifier,
    bytes makerAssetData,
    bytes takerAssetData,
    bytes signature)
onlyManager notShutDown`

Please see parameter descriptions above. Of note are the parameters:

  `orderValues[6]` - The fill quantity of taker asset token.
  `identifier` - The active order's orderID.

This public function must ensure that:

  the fund is not shut down,
  the `msg.sender` is the fund manager.
  the fund is not filling its own make order,
  the asset token price pair exists in the price source,
  the asset token prices are recent,
  applicable risk policies are evaluated,
  asset tokens to be traded are approved (if required),
  the take order transaction is sent to the exchange contract,
  the order is removed from the exchange contract (if possible),
  the acquired asset token, if not already held, is added to `ownedAssets`.

The function takes the opposite side of an existing make order on the OasisDex Matching Market exchange contract, delivering the asset token demanded by the make order in exchange for the asset token supplied by the make order, in the quantities (or less for partial order fills) specified. Finally, the function returns acquired token assets to the fund's vault and updates the fund's internal order tracking.
&nbsp;

`function cancelOrder(
    address targetExchange,
    address[6] orderAddresses,
    uint[8] orderValues,
    bytes32 identifier,
    bytes wrappedMakerAssetData,
    bytes takerAssetData,
    bytes signature
) onlyCancelPermitted(targetExchange, orderAddresses[2])`

Please see parameter descriptions above. Of note are the parameters:

  `orderAddresses[2]` - The order maker asset token.
  `identifier` - The active order's orderID.

This public function cancels an existing order on the OasisDex Matching Market exchange contract by calling the  OasisDex Matching Market contract's `cancel()` function. The function applies the `onlyCancelPermitted` modifier, allowing the cancel to only be submitted under one of these conditions: the fund manager cancels the order, the fund is shut down or the order has expired. The asset token is finally removed from the water<b>melon</b> fund's internal order tracking and the maker asset token is returned to the water<b>melon</b> fund's vault.
&nbsp;

`function getOrder(
    address targetExchange,
    uint id,
    address makerAsset)
view returns (
    address,
    address,
    uint,
    uint)`

This public view function returns the current relevant order information (maker asset token address, taker asset token address, maker asset token quantity and taker asset token quantity) given the exchange contract address, the order identifier and the maker asset token contract address parameters. Orders which have been partially filled will return the remaining, respective token quantities.
&nbsp;
