# trading

## Trading.sol

Description
...

Inherits from Spoke, DSMath, TradingInterface (link)

On Construction

The contract requires the hug address, an array of exchange addresses, an array of exchange adapter addresses and an array of booleans indicating if an exchange takes custody of tokens for make orders. The contract becomes a spoke to the hub. The constructor of the trading contract requires that the length of the exchange array matches the length of exchange adapter array and the length of the exchange array matches the length of boolean custody status array. Finally, the constructor builds the public variable `exchanges[]` array of `Exchange` structs.



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


Public State variables

    `Exchange[] public exchanges`

    A public array of `Exchange` structs which stores all exchanges with which the fund has been initialized.


    `Order[] public orders`

    A public array of `Order` structs which stores all active orders [CHECK] on the  


    `mapping (address => bool) public exchangeIsAdded`

    A public mapping which indicates that a specific exchange (as identified by the exchange address) is registered for the fund.


    `mapping (address => mapping(address => OpenMakeOrder)) public exchangesToOpenMakeOrders`

    A public compound mapping associating an exchange address to open make orders from the fund on the specific exchange.

    mapping (address => bool) public isInOpenMakeOrder;

    uint public constant ORDER_LIFESPAN = 1 days;

Public functions



    // TODO: who can add exchanges? should they just be set at creation?
    function addExchange(address _exchange, address _adapter, bool _takesCustody) internal {
        // require(CanonicalRegistrar(routes.canonicalRegistrar).exchangeIsRegistered(_exchange));
        require(!exchangeIsAdded[_exchange]);
        exchangeIsAdded[_exchange] = true;
        exchanges.push(Exchange(_exchange, _adapter, _takesCustody));
    }

    function callOnExchange(
        uint exchangeIndex,
        bytes4 method,
        address[5] orderAddresses,
        uint[8] orderValues,
        bytes32 identifier,
        uint8 v,
        bytes32 r,
        bytes32 s
    )
        external
    {
        PolicyManager(routes.policyManager).preValidate(method, [orderAddresses[0], orderAddresses[1], orderAddresses[2], orderAddresses[3], exchanges[exchangeIndex].exchange], [orderValues[0], orderValues[1], orderValues[6]], identifier);
        // require(CanonicalRegistrar(routes.canonicalRegistrar).exchangeMethodIsAllowed(exchanges[exchangeIndex].exchange, method));
        address adapter = exchanges[exchangeIndex].adapter;
        address exchange = exchanges[exchangeIndex].exchange;
        require(adapter.delegatecall(
            method, exchange, orderAddresses, orderValues, identifier, v, r, s
        ));
        PolicyManager(routes.policyManager).postValidate(method, [orderAddresses[0], orderAddresses[1], orderAddresses[2], orderAddresses[3], exchanges[exchangeIndex].exchange], [orderValues[0], orderValues[1], orderValues[6]], identifier);
    }

    function addOpenMakeOrder(
        address ofExchange,
        address ofSellAsset,
        uint orderId
    ) delegateInternal {
        require(!isInOpenMakeOrder[ofSellAsset]);
        isInOpenMakeOrder[ofSellAsset] = true;
        exchangesToOpenMakeOrders[ofExchange][ofSellAsset].id = orderId;
        exchangesToOpenMakeOrders[ofExchange][ofSellAsset].expiresAt = add(block.timestamp, ORDER_LIFESPAN);
    }

    function removeOpenMakeOrder(
        address ofExchange,
        address ofSellAsset
    ) delegateInternal {
        delete exchangesToOpenMakeOrders[ofExchange][ofSellAsset];
    }

    function orderUpdateHook(
        address ofExchange,
        bytes32 orderId,
        UpdateType updateType,
        address[2] orderAddresses,
        uint[3] orderValues
    ) delegateInternal {
        // only save make/take
        // TODO: change to more generic datastore when that shift is made generally
        if (updateType == UpdateType.make || updateType == UpdateType.take) {
            orders.push(Order({
                exchangeAddress: ofExchange,
                orderId: orderId,
                updateType: updateType,
                makerAsset: orderAddresses[0],
                takerAsset: orderAddresses[1],
                makerQuantity: orderValues[0],
                takerQuantity: orderValues[1],
                timestamp: block.timestamp,
                fillTakerQuantity: orderValues[2]
            }));
        }
    }

    function updateAndGetQuantityBeingTraded(address _asset) returns (uint) {
        uint quantityHere = ERC20(_asset).balanceOf(this);
        return add(updateAndGetQuantityHeldInExchange(_asset), quantityHere);
    }

    function updateAndGetQuantityHeldInExchange(address ofAsset) returns (uint) {
        uint totalSellQuantity; // quantity in custody across exchanges
        uint totalSellQuantityInApprove; // quantity of asset in approve (allowance) but not custody of exchange
        for (uint i; i < exchanges.length; i++) {
            if (exchangesToOpenMakeOrders[exchanges[i].exchange][ofAsset].id == 0) {
                continue;
            }
            address sellAsset;
            uint sellQuantity;
            (sellAsset, , sellQuantity, ) = GenericExchangeInterface(exchanges[i].adapter).getOrder(exchanges[i].exchange, exchangesToOpenMakeOrders[exchanges[i].exchange][ofAsset].id);
            if (sellQuantity == 0) {    // remove id if remaining sell quantity zero (closed)
                delete exchangesToOpenMakeOrders[exchanges[i].exchange][ofAsset];
            }
            totalSellQuantity = add(totalSellQuantity, sellQuantity);
            if (!exchanges[i].takesCustody) {
                totalSellQuantityInApprove += sellQuantity;
            }
        }
        if (totalSellQuantity == 0) {
            isInOpenMakeOrder[ofAsset] = false;
        }
        return sub(totalSellQuantity, totalSellQuantityInApprove); // Since quantity in approve is not actually in custody
    }

    function returnToVault(ERC20[] _tokens) public {
        for (uint i = 0; i < _tokens.length; i++) {
            _tokens[i].transfer(Vault(routes.vault), _tokens[i].balanceOf(this));
        }
    }
}


document factory?
