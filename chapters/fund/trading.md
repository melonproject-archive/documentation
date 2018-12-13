# Trading

The Melon Protocol integrates with decentralized exchanges to facilitate the trading of Assets, one of the essential functionalities of a Melon Fund. This means that a Melon Fund must accommodate multiple different decentralized exchanges smart contracts if the fund is to draw from a wider pool of liquidity.

## Trading.sol

Description

The Trading contract is created by the tradingFactory, which holds a queryable array of all created Trading contracts and emits the `instanceCreated` event along with the trading contract's address.

The trading contract:

-manages the set up of the interface infrastructure to the various selected exchanges.

-manages open make orders that the fund has submitted to the various registered exchanges.

-houses the common function for calling specific exchange functions through each exchange's respective exchange adapters. Note that each exchange will have their own unique interface and required parameters, which the adapters accommodate.

-provides a public function to transfer an array of token assets to the fund's vault.

-provides view functions to get order information regarding specific assets on specific exchanges as well as specific order detail.

The contract has a fallback function to retrieve ETH.

Inherits from
Spoke, DSMath, TradingInterface (link)

On Construction

The contract requires the hub address, an array of exchange addresses, an array of exchange adapter addresses and an array of booleans indicating if an exchange takes custody of tokens for make orders. The contract becomes a spoke to the hub. The constructor of the trading contract requires that the length of the exchange array matches the length of exchange adapter array and the length of the exchange array matches the length of boolean custody status array. Finally, the constructor builds the public variable `exchanges[]` array of `Exchange` structs.

Structs

`Exchange`
Member Variables

`address exchange` - The address of the decentralized exchange smart contact.

`address adapter` - The address of the corresponding adapter smart contract.

`bool takesCustody` - An flag specifying whether the exchange holds the asset token in its own custody for make orders.

`Order`
Member Variables

`address exchangeAddress` - The address of the decentralized exchange smart contact.

`bytes32 orderId` - A unique identifier for a specific order on the specific exchange.

`UpdateType updateType` - A struct indicating the update behavior/action of the order. Permitted values are `make`, `take` and `cancel`.

`address makerAsset` - The address of the asset token owned and provided in exchange.

`address takerAsset` - The address of the asset token to be received in exchange.

`uint makerQuantity` - An integer representing the quantity of the asset token owned and provided in exchange.

`uint takerQuantity` - An integer representing the quantity of the asset token owned and provided in exchange.

`uint timestamp` - The timestamp of the block in which the submitted order transaction was mined.

`uint fillTakerQuantity` - An integer representing the quantity of the maker asset token traded in the make order. This value is not necessarily the same as the `makerQuantity` because taker participants can choose to only partially execute the order, i.e. take a lower quantity of the `makerAsset` token than specified by the order's `makerQuantity`.

`OpenMakeOrder`
Member Variables

    `uint id` - An integer originating from the exchange which uniquely identifies the order within the exchange.

    `uint expiresAt` - An integer representing the time when the order expires. The timestamp is represented by the Ethereum blockchain as a UNIX Epoch. After order expiration, the order will no longer [exist/be active] on the exchange and custody, if the exchange held custody, returns from the exchange contract to the fund contract. CHECK

Enums

`UpdateType` - An enum which characterizes the type of update to the order.

Member Types

`make` - Indicates a make order type, where a quantity of a specific asset is offered at a specified price.

`take` - Indicates a take order type, where the offered quantity (or less) of a specific asset is accepted at the specified price by the counterparty. Taking less than the offered quantity in the order would be considered a "partial fill" of the order.

`cancel` - A type indicating that the update performed will cancel the order.

`swap`- A type specific to the Kyber exchange adapter, similar in functionality to `take` described above.

Public State variables

    `Exchange[] public exchanges`

    A public array of `Exchange` structs which stores all exchanges with which the fund has been initialized.


    `Order[] public orders`

    A public array of `Order` structs which stores all active orders [CHECK] on the


    `mapping (address => bool) public exchangeIsAdded`

    A public mapping which indicates that a specific exchange (as identified by the exchange address) is registered for the fund.


    `mapping (address => mapping(address => OpenMakeOrder)) public exchangesToOpenMakeOrders`

    A public compound mapping associating an exchange address to open make orders from the fund on the specific exchange.

    `mapping (address => bool) public isInOpenMakeOrder`

    A public mapping indicating that the specified token asset is currently offered in an open make order on an exchange.

    `uint public constant ORDER_LIFESPAN = 1 days`

    A public constant specifying the number of seconds that an order will remain active on an exchange. This number is added to the order creation date's timestamp to fully specify the order's expiration date. `1 days` is equal to 86400 ( 60 * 60 * 24 ).

Modifiers

    `delegateInternal()`

    A modifier which requires that the caller (`msg.sender`) is the current contract `Trading.sol`. This ensures that only the current contract can call a function implementing this modifier.

Public functions

    `function addExchange(address _exchange, address _adapter, bool _takesCustody) internal`

    This is an internal function which is called for each exchange address passed to the constructor, adding the full exchange struct to the exchanges array state variable. The function ensures that an exchange has not previously been registered.


    `function callOnExchange(
        uint exchangeIndex,
        string methodSignature,
        address[6] orderAddresses,
        uint[8] orderValues,
        bytes32 identifier,
        bytes makerAssetData,
        bytes takerAssetData,
        bytes signature
    ) public`


    This is the fund's general interface to each registered exchange for trading asset tokens. The client will call this function for specific exchange/trading interactions. This function first calls the policyManager to ensure that function-specific policies are pre- or post-executed to ensure that the exchange trade adheres to the policies configured for the fund.  [Get more detail as melon.js matures]



    `function addOpenMakeOrder(
        address ofExchange,
        address ofSellAsset,
        uint orderId
    ) delegateInternal`

    This function can only be called from within the current contract. The function ensures that the sell asset token does not already have a current open sell order and that there are one or more orders in the `orders` array. The function then sets the `isInOpenMakeOrder` mapping for the sell asset token address to `true`, and sets the details of the address's `openMakeOrder` struct on the contracts `exchangesToOpenMakeOrders` mapping. [Why require >1 orders??]


    `function removeOpenMakeOrder(
        address ofExchange,
        address ofSellAsset
    ) delegateInternal`

    This function can only be called from within the current contract. The function removes the provided sell asset token address entry for the provided exchange address.


    `function orderUpdateHook(
        address ofExchange,
        bytes32 orderId,
        UpdateType updateType,
        address[2] orderAddresses,
        uint[3] orderValues
    ) delegateInternal`

    This function can only be called from within the current contract. The function used the input parameters and the current execution block's timestamp to push make- or take orders to the `orders` array. [Why only make or take orders??]

    `function updateAndGetQuantityBeingTraded(address _asset) returns (uint)`

    This function returns the sum of the quantity of the provided asset token address held by the current contract and the quantity of the provided asset token held across all registered exchanges in the fund's make orders. The sum returned excluded quantities in make orders where the exchange does not take custody of the tokens.


    `function updateAndGetQuantityHeldInExchange(address ofAsset) returns (uint)`

    This function sums and returns all quantities of the provided asset token address in make orders across all registered exchanges, excluding, however, quantities in make orders where the exchange does not take custody of the tokens, but uses the ERC-20 "approve" functionality. The rationale is that token quantities in "approve" status are not actually held by the exchange. The function also maintains the `exchangesToOpenMakeOrders` and `isInOpenMakeOrder` mappings.


    `function returnToVault(ERC20[] _tokens) public`

    This public function transfers all token quantities of the provided array of token asset addresses from the current current contract's custody back to the fund's vault.

## Exchange Adapters

Exchange Adapters are smart contracts which communicate directly and on-chain with the intended DEX smart contract. They serve as a translation bridge between the Melon Fund and the DEX.

Currently, the Melon Protocol has adapters to integrate the following DEXs:

- Oasis DEX
- 0x (enabling interaction on the orderbooks of all 0x relayers)
- Kyber Network
- Ethfinex

Each exchange is tied to a specific adapter by the canonical registrar. A fund can be setup to use multiple exchanges, provided they are registered by the registrar.

## Adapter Function Details

The `Fund.sol` smart contract in the Melon Protocol (the blockchain fund instance) commonly uses the following functions in the Exchange Adapter to interact with the intended DEX for trading purposes:

- `makeOrder()` Creates a new order in the DEX's order book. The order may not be immediately executed. Note that this function will not be implemented for the 0x adapter until 0x Version 2 is released.

- `takeOrder()` Represents implicit agreement with a standing make order on the DEX's order book. The order will be immediately executed.

- `cancelOrder()` Retracts a standing make order from the DEX's order book. The cancelation will be immediately executed.

A single event is emitted by the Exchange Adapter:

- `OrderUpdated()` Event to inform other layers (e.g. web page) that the order has been updated in some way.

The following functions are public view functions:

- `getLastOrderId()` - Constant view function which returns the last order Id on a specific exchange.

- `getOrder()` - Constant view function which returns the order's sell asset address, buy asset address, sell asset quantity and buy asset quantity on a given exchange for a given order Id.

Note that fund is not limited to these functions and can call arbitrary functions on the exchange adapters using delegate calls, provided the function signature is whitelisted by the canonical registrar.
