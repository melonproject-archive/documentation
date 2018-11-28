# Hub/Spoke

## General

The Hub and Spoke contracts make up the core contract framework of the Melon fund. Hub and Spoke is an architecture design to help reason about all the moving parts of fund as constructed by an interconnected system of linked smart contracts. The Hub will contain all relevant information for all Spokes registered with the Hub. Each Spoke will independently contain all relevant information about the Hub. Individual Spokes will have programmatic access to information in specific other Spokes.

In general, there is one Hub contract and separate, distinct Spoke contracts for each fund. (CHECK: Implementation for shared components as spoke) The Hub forms the core of the individual Melon fund with each Spoke contributing specific services to the fund. The Hub contract stores the manager's address and the fund name permanently in state, and also contains the functionality to permanently and irreversibly shut the Melon fund down.


## Hub.sol

#### Description

The Hub maintains the specific Spoke components of the fund in terms of their routing and their permissioning. Permissioning is of utmost importance as it ensures only design-intended access to specific components of the Melon fund smart contract system. The permissioning has a very low-level granularity, exclusively permitting only specific Spokes to invoke specific functions on specific target Spokes. This surgical permissioning system ensures that sensitive function execution can occur as intended and not by external actors.

#### Inherits from

DSGuard (link)

#### On Construction

The constructor requires and sets the manager address and the fund name.


#### Structs

`Settings`

  Member variables:  

  `address accounting`
  `address feeManager`
  `address participation`
  `address policyManager`
  `address shares`
  `address trading`
  `address vault`
  `address priceSource`
  `address canonicalRegistrar`
  `address version`
  `address engine`
  `address mlnAddress`

This struct stores the contract addresses of the listed components and spokes.

#### Enums

None.

#### Modifiers

None.

#### Events

None.

#### Public State Variables

`Settings public settings`

A `Settings` struct containing Spoke and component addresses.


`address public manager`

The address of the fund manager.


`string public name`

The name of the fund as defined by the manager at fund set up.


`bool public isShutDown`

A boolean variable defining the operational status of the fund.


`bool public spokesSet`

A boolean variable defining the completeness status of all spoke settings.


`bool public routingSet`

A boolean variable defining the completeness status of all routing settings.


`bool public permissionsSet`

A boolean variable defining the completeness status of all permissions settings.


#### Public Functions


`function shutDownFund() public`

This function sets the `ìsShutDown` state variable to `true`, effectively disabling the fund for all activities except investor redemptions. This function can only be successfully called by the manager address. The function's actions are permanent and irreversible.


`function setSpokes(address[12] _spokes)`

This function takes an array of addresses and sets all member variables of the `settings` state variable struct if they have not already been set as part of the fund initialization sequence. The function then sets the `spokesSet` state variable to `true`.


`function setRouting()`

This function requires that the Spokes and Routings have been set. It then initializes (see Spoke `initialize()` below) all registered Spokes and finally sets the `routingSet` state variable to `true`.


`function setPermissions()`

This function requires that the Spokes and Routings have been set, but that permissions have not been set. Next, the functions *explicitly* sets specific, design-intended permissions between the calling contracts and called contracts' functions. The function then finally sets the `permissionsSet` state variable to `true`.


`function vault() view returns (address)`

This view function returns the vault Spoke address.


`function accounting() view returns (address)`

This view function returns the accounting Spoke address.


`function priceSource() view returns (address)`

This view function returns the priceSource contract address.


`function participation() view returns (address)`

This view function returns the participation Spoke address.


`function trading() view returns (address)`

This view function returns the trading Spoke address.


`function shares() view returns (address)`

This view function returns the shares Spoke address.


`function policyManager() view returns (address)`

This view function returns the policyManager Spoke address.


## Spoke.sol

#### Description

All Spoke contracts are registered with only one Hub and are initialized, storing the routes to all other Spokes registered with the Hub. Spokes serve as an abstraction of functionality and each Spoke has distinct and separate business logic domains, but may pragmatically access other Spokes registered with their Hub.

#### Inherits from

DSAuth (link)

#### On Construction

Sets the `hub` state variable and sets the the `hub` as the `authority` as defined for permissioning within DSAuth.

#### Structs

`Routes`

  Member variables:

  `address accounting`
  `address feeManager`
  `address participation`
  `address policyManager`
  `address shares`
  `address trading`
  `address vault`
  `address priceSource`
  `address canonicalRegistrar`
  `address version`
  `address engine`
  `address mlnAddress`

This struct stores the contract addresses of the listed components and spokes.


#### Enums

None.

#### Modifiers

None.

#### Events

None.

#### Public State Variables

`Hub public hub`

A variable of type `Hub`, defining the specific Hub contract to which the Spoke is connected.


`Routes public routes`

A `Routes` struct storing all initialized Spoke and component addresses.


`bool public initialized`

A boolean variable defining the initialization status of the Spoke.


#### Public Functions

`function initialize(address[10] _spokes)`

This function requires firstly that the Spoke not be initialized, then takes an array of addresses of all Spoke- and component contracts, sets all members of the `routes` struct state variable and finally sets `initialized` = `true`.

`function engine() view returns (address)`

This view function returns the engine component contract address.

`function mlnAddress() view returns (address)`

This view function returns the MLN token contract address.

`function priceSource() view returns (address)`

This view function returns the priceSource component contract address.

`function version() view returns (address)`

This view function returns the version component contract address.
