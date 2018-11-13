# Hub

## Hub.sol

Description
...

Inherits from DSGuard (link)

On Construction
Sets the manager address and the fund name.


Structs

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

This struct stores the contract addresses of the listed components and spokes.

Enums

None.

Public State Variables

`Settings public settings`

A struct containing defined hub settings addresses.


`address public manager`

The address of the fund manager.


`string public name`

The name of the fund as defined by the manager on fund set up.


`bool public isShutDown`

A boolean variable defining the operational status of the fund.


`bool public spokesSet`

A boolean variable defining the completeness status of all spoke settings.


`bool public routingSet`

A boolean variable defining the completeness status of all routing settings.


`bool public permissionsSet`

A boolean variable defining the completeness status of all permissions settings.


Public Functions


`function shutDownFund() public`

This function sets `ìsShutDown` to `true` effectively disabling the fund for all activities except investor redemptions. This function can only be successfully called by the manager address.


`function setSpokes(address[10] _spokes)`

This function takes an array of addresses and sets all member variables of the `settings` state variable struct if they have not already been set as part of the fund initialization sequence.


`function setRouting()`

This function sets the routing of the spokes after the spokes have been set, then set `routingSet`= `true`.


`function setPermissions()`

This function sets various permissions between spokes requiring the defined permission, then sets `permissionsSet`= `true`.


`function vault() view returns (address)`

This function returns the vault spoke address.


`function accounting() view returns (address)`

This function returns the accounting spoke address.


`function priceSource() view returns (address)`

This function returns the priceSource spoke address.


`function participation() view returns (address)`

This function returns the participation spoke address.


`function trading() view returns (address)`

This function returns the trading spoke address.


`function shares() view returns (address)`

This function returns the shares spoke address.


`function policyManager() view returns (address)`

This function returns the policyManager spoke address.


## Spoke.sol

Description
...

Inherits from DSAuth (link)

On Construction
Sets the hub address and sets the authority to the hub address.

Structs

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

This struct stores the contract addresses of the listed components and spokes.


Enums

None.

Public State Variables

`Hub public hub`

A variable of type `Hub`.


`Routes public routes`

A struct storing all initialized spoke and component addresses.


`bool public initialized`

A boolean variable defining the initialization status of the spoke.


Public Functions

`function initialize(address[10] _spokes)`

This functions takes an array of addresses of all spoke contracts, sets all members of the `routes` struct state variable and finally sets `initialized` = `true`.
