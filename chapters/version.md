# Version

## General

The Version contract is a singleton contract for all funds deployed under a specific Melon protocol version. All Factory contracts are exclusive to the Version contract and cannot be changed once set.

&nbsp;

## Version.sol

#### Description

The Version contract inherits from FundFactory and is the single, exclusive FundFactory contract for a specific Melon Protocol version. The Version contract is the contract creator for all funds of the specific Melon Protocol version.

#### Inherits from

FundFactory, DSAuth, VersionInterface (link)

&nbsp;

#### On Construction

The following contract addresses are provided as parameters:

`address _accountingFactory` - The address of the AccountingFactory contract.
`address _feeManagerFactory` - The address of the FeeManagerFactory contract.
`address _participationFactory` - The address of the ParticipationFactory contract.
`address _sharesFactory` - The address of the SharesFactory contract.
`address _tradingFactory` - The address of the TradingFactory contract.
`address _vaultFactory` - The address of the VaultFactory contract.
`address _policyManagerFactory` - The address of the PolicyManagerFactory contract.
`address _engine` - The address of the Melon Engine contract.
`address _priceSource` - The address of the PriceSource contract.
`address _mlnAddress` - The address of the Melon Token contract.
`address _registry` - The address of the Registry contract.

These address parameters set the corresponding state variables as defined in the inherited definition of  `FundFactory`. The constructor then directly sets the `registry` state variable with the address of the registry contract.

The Version contract assumes the Governance contract is the deployer.

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

`uint public amguPrice`

A public state variable specifying the current setting for the Asset Management Gas Unit (AMGU) price. This setting can be changed at the discretion of the Melon Technical Council (MTC).

`bool public isShutDown`

A public state variable specifying the operating state of this specific Melon Protocol version.

&nbsp;

#### Public Functions

`function setAmguPrice(uint _price) auth`

This function requires that the caller is the `owner` or the current contract. The function sets the `amguPrice` state variable with the provided parameter.


`function shutDown() external auth`

[Wait for final functionality]

`function shutDownFund(address _fund) external`

This function first requires that either the Version contract is shut down or that `msg.sender` the target fund's address. The function calls the target fund's `shutDownFund()` function. When the Version contract is shut down, any external call may shut down a specific fund.


`function getAmguPrice() returns (uint)`

This function returns the current setting of the `amguPrice` state variable.


`function isFund(address _who) returns (bool)`

This function returns `true` if the address parameter provided is a fund contract created under the specific Version contract. The function returns `false` otherwise.


`function isFundFactory(address _who) returns (bool)`

This function returns `true` if the address parameter provided is the specific Version contract. The function returns `false` otherwise.
