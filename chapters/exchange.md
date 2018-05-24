# Exchanges and Adapters

The Melon Protocol integrates with exchanges to facilitate the trading of Assets, one of the essential functionalities of a Melon Fund.

Exchanges can be roughly classified as Centralized and Decentralized.

## Centralized Exchanges

Centralized Exchanges are the exchanges that have been in use since the beginning of asset trading. They are setup and run by third parties and offer the service of trading assets to individual clients. Centralized Exchanges manage accounts for individual investors and their assets, essentially providing a matchmaking and custody service.

Investors must put their full faith and trust in the exchange's operators and the institutions that regulate them. Security considerations exist over the operators' willful mishandling of investor funds, innocent mistakes and poor security measures/procedures which malicious parties exploit as "hacks". Further, these exchanges and their investors must also trust internal staff.

Investor assets are often stored communally at known addresses, making them an attractive target for nefarious actors.

## Decentralized Exchanges

Decentralized Exchanges (DEX) are an entirely new phenomenon and only exist due to blockchain and smart contract technology. They are characterized by having matching and custody functionality entirely on the blockchain. This means that the decentralized exchange's smart contract itself holds custody of investors' assets. Investors ultimately retain power over their assets, even if not their actual custody. Trades are executed as atomic swaps, meaning that the two assets involved in the trade are transactionally transferred as a single unit of work, i.e. either both assets transfer successfully, or none.

The DEX's security is only as good as its smart contract's integrity. However, the greater the value transacted over the DEX, the greater the confidence in the DEX's security becomes.

DEXs maintain their order books (list of live buy- and sell orders) either on-chain, off-chain, or both as a hybrid. Off-chain order books boast speed and efficiency, while the on-chain variety are more secure.

The Melon Protocol will utilize DEXs for the actual trading infrastructure of Melon Funds. Retaining control of assets within Melon Funds is a defining characteristic of the platform and paramount to its success.

## DEX Smart Contracts

Each DEX implements their designed trading behavior individually, although some standards have appeared for the relaying of orders (e.g. 0x Protocol). This means that a Melon Fund must accommodate multiple different DEX smart contracts if the fund is to draw from a wider pool of liquidity.

## Exchange Adapters

Exchange Adapters are smart contracts which communicate directly and on-chain with the intended DEX smart contract. They serve as a translation bridge between the Melon Fund and the DEX.

Currently, the Melon Protocol has adapters to integrate the following DEXs:

- Oasis DEX
- 0x (enabling interaction with all 0x relayers)

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
