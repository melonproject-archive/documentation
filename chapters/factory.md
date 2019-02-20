# Factory

## General

Factory contracts are utilities for the creation of the many component contracts and infrastructure providing services to water<b>melon</b> funds.
&nbsp;

## Factory.sol

#### Description

The contract is a base contract which defines base elements below. It is inherited by all factory contracts.
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

None.

&nbsp;

#### Events

`event NewInstance()`

    `address indexed hub` - The address of the `hub` corresponding to the created contract.
    `address indexed instance) - The address of the created contract.

This event is triggered when a the Factory creates a new contract. The parameters listed above are logged with the event.
&nbsp;

#### Public State Variables

`mapping (address => bool) public childExists`

This public mapping state variable maps an address to boolean. It is used to determine whether an address is that of a contract created by the Factory.
&nbsp;

#### Public Functions

`function isInstance(address _child) public view returns (bool)`

This public view function returns a boolean indicating whether the address provided corresponds to a contract created by the `Factory` contract.
&nbsp;

## FundFactory.sol

#### Description

This contract manages and executes the orderly creation of a new water<b>melon</b> fund, linking all aspects of components, settings, routings and permissions.
&nbsp;

#### Inherits from

Factory, AmguConsumer (link)
&nbsp;

#### On Construction

The contract constructor requires the following address parameters and sets the corresponding address state variables:

`address _accountingFactory` - The address of the Version's `AccountingFactory` contract.
`address _feeManagerFactory` - The address of the Version's `FeeManagerFactory` contract.
`address _participationFactory` - The address of the Version's `ParticipationFactory` contract.
`address _sharesFactory` - The address of the Version's `SharesFactory` contract.
`address _tradingFactory` - The address of the Version's `TradingFactory` contract.
`address _vaultFactory` - The address of the Version's `VaultFactory` contract.
`address _policyManagerFactory` - The address of the Version's `PolicyManagerFactory` contract.
`address _registry` - The address of the Version's `Registry` contract.
`address _version` - The address of the `Version` contract.
&nbsp;

#### Structs

`Settings`

  Member variables:

  `string name` - The name of the water<b>melon</b> fund.
  `address[] exchanges` - An array of exchange contract addresses registered for the water<b>melon</b> fund.
  `address[] adapters` - An array of exchange adapter contract addresses registered for the water<b>melon</b> fund.
  `address denominationAsset` - The address of the asset token in which the water<b>melon</b> fund is denominated.
  `address nativeAsset` - The address of the asset token designated as the water<b>melon</b> fund's native asset, i.e. the water<b>melon</b> fund's native network token.
  `address[] defaultAssets` - An array of addresses designating the water<b>melon</b> fund's accepted asset tokens for subscription.
  `bool[] takesCustody` - An array of booleans mirroring an exchange contract's `takesCustody` state variable.
  `address priceSource` - The address of the water<b>melon</b> fund's price source.
  `address[] fees` - An array of addresses of the water<b>melon</b> fund's registered `fee` contracts.
  `uint[] feeRates` - An array of integers representing the fee rates registered for the water<b>melon</b> fund.
  `uint[] feePeriods` - An array of integer representing the the duration of a fee performance measurement period in seconds.
&nbsp;

#### Enums

None.

&nbsp;

#### Modifiers

`modifier componentNotSet(address _component)`  

This modifier enables a function to enforce the single execution of the implementing function and is evaluated prior to executing the implementing function's functionality.
&nbsp;

`modifier componentSet(address _component)`  

This modifier requires that the provided `_component` is set, i.e. exists.
The condition is evaluated prior to executing the implementing function's functionality.
&nbsp;

#### Events

NewFund()

    `address indexed manager` - The address of the water<b>melon</b> fund's manager.
    `address indexed hub` - The address of the water<b>melon</b> fund's `hub`.
    `address[12] routes` - An array of 12 address representing relevant water<b>melon</b> fund component addresses.

This event is emitted when the FundFactory creates a new fund. The event logs the parameters listed above.
&nbsp;

#### Public State Variables

`VersionInterface public version`

This public state variable represents the Version contract.
&nbsp;

`address public registry`

This public state variable represents the Version's Registry contract.
&nbsp;

`AccountingFactory public accountingFactory`

This public state variable represents the Version's AccountingFactory contract.
&nbsp;

`FeeManagerFactory public feeManagerFactory`

This public state variable represents the Version's FeeManagerFactory contract.
&nbsp;

`ParticipationFactory public participationFactory`

This public state variable represents the Version's ParticipationFactory contract.
&nbsp;

`PolicyManagerFactory public policyManagerFactory`

This public state variable represents the Version's PolicyManagerFactory contract.
&nbsp;

`SharesFactory public sharesFactory`

This public state variable represents the Version's SharesFactory contract.
&nbsp;

`TradingFactory public tradingFactory`

This public state variable represents the Version's TradingFactory contract.
&nbsp;

`VaultFactory public vaultFactory`

This public state variable represents the Version's VaultFactory contract.
&nbsp;

`address[] public funds`

This public state variable address array represents all fund contract addresses created by the FundFactory.
&nbsp;

`mapping (address => address) public managersToHubs`

This public state variable mapping maps an address to an address, creating a relationship of manager to `hub`.
&nbsp;

`mapping (address => Hub.Routes) public managersToRoutes`

This public state variable mapping maps an address to a `Routes` struct, creating a relationship of manager to `Routes`.
&nbsp;

`mapping (address => Settings) public managersToSettings`

This public state variable mapping maps an address to `Settings` struct, creating a relationship of manager to `Settings`. This mapping is only used internally.
&nbsp;

#### Public Functions

`function beginSetup(
    string _name,
    address[] _fees,
    uint[] _feeRates,
    uint[] _feePeriods,
    address[] _exchanges,
    address[] _adapters,
    address _denominationAsset,
    address _nativeAsset,
    address[] _defaultAssets,
    bool[] _takesCustody
) public componentNotSet(managersToHubs[msg.sender])`

This public function takes the following parameters and initiates the set up process of a water<b>melon</b> fund. The function implements the `componentNotSet()` modifier ensuring its one-time execution in the water<b>melon</b> fund setup process. The function requires that the Version is not shut down. The function then creates a new `Hub` owned by `msg.sender` with the `name` provided. The `Hub` is then added to the `managersToHubs` mapping, mapped to the `msg.sender` (manager). A `Settings` struct is constructed from the requisite parameter values and is added to the `managersToSettings` mapping, mapped to the `msg.sender` (manager). Finally, the function the proceeds to populate the `managersToRoutes` mapping by mapping the `msg.sender` (manager) address to the following individual `Routes` elements: `priceSource`, `registry`, `version`, `engine` and `mlnToken`.
&nbsp;

`function createAccounting()
    external
    componentSet(managersToHubs[msg.sender])
    componentNotSet(managersToRoutes[msg.sender].accounting)
    amguPayable(false)
    payable`

This external function creates the Accounting spoke of a water<b>melon</b> fund and adds the resulting contract address to the `managersToRoutes` mapping, mapped to the `msg.sender` (manager). The function implements the `componentNotSet()` modifier ensuring its one-time execution in the water<b>melon</b> fund setup process. The function is `payable` and also implements the `amguPayable` modifier, requiring amgu payment. Here, the `deductIncentive` parameter is set to `false`.
&nbsp;

`function createFeeManager()
        external
        componentSet(managersToHubs[msg.sender])
        componentNotSet(managersToRoutes[msg.sender].feeManager)
        amguPayable(false)
        payable`

This external function creates the FeeManager spoke of a water<b>melon</b> fund and adds the resulting contract address to the `managersToRoutes` mapping, mapped to the `msg.sender` (manager). The function implements the `componentNotSet()` modifier ensuring its one-time execution in the water<b>melon</b> fund setup process. The function is `payable` and also implements the `amguPayable` modifier, requiring amgu payment. Here, the `deductIncentive` parameter is set to `false`.
&nbsp;

`function createParticipation()
        external
        componentSet(managersToHubs[msg.sender])
        componentNotSet(managersToRoutes[msg.sender].participation)
        amguPayable(false)
        payable`

This external function creates the Participation spoke of a water<b>melon</b> fund and adds the resulting contract address to the `managersToRoutes` mapping, mapped to the `msg.sender` (manager). The function implements the `componentNotSet()` modifier ensuring its one-time execution in the water<b>melon</b> fund setup process. The function is `payable` and also implements the `amguPayable` modifier, requiring amgu payment. Here, the `deductIncentive` parameter is set to `false`.
&nbsp;

`function createPolicyManager()
    external
    componentSet(managersToHubs[msg.sender])
    componentNotSet(managersToRoutes[msg.sender].policyManager)
    amguPayable(false)
    payable`

This external function creates the PolicyManager spoke of a water<b>melon</b> fund and adds the resulting contract address to the `managersToRoutes` mapping, mapped to the `msg.sender` (manager). The function implements the `componentNotSet()` modifier ensuring its one-time execution in the water<b>melon</b> fund setup process. The function is `payable` and also implements the `amguPayable` modifier, requiring amgu payment. Here, the `deductIncentive` parameter is set to `false`.
&nbsp;

`function createShares()
    external
    componentSet(managersToHubs[msg.sender])
    componentNotSet(managersToRoutes[msg.sender].shares)
    amguPayable(false)
    payable`

This external function creates the Shares spoke of a water<b>melon</b> fund and adds the resulting contract address to the `managersToRoutes` mapping, mapped to the `msg.sender` (manager). The function implements the `componentNotSet()` modifier ensuring its one-time execution in the water<b>melon</b> fund setup process. The function is `payable` and also implements the `amguPayable` modifier, requiring amgu payment. Here, the `deductIncentive` parameter is set to `false`.
&nbsp;

`function createTrading()
    external
    componentSet(managersToHubs[msg.sender])
    componentNotSet(managersToRoutes[msg.sender].trading)
    amguPayable(false)
    payable`

This external function creates the Trading spoke of a water<b>melon</b> fund and adds the resulting contract address to the `managersToRoutes` mapping, mapped to the `msg.sender` (manager). The function implements the `componentNotSet()` modifier ensuring its one-time execution in the water<b>melon</b> fund setup process. The function is `payable` and also implements the `amguPayable` modifier, requiring amgu payment. Here, the `deductIncentive` parameter is set to `false`.
&nbsp;

`function createVault()
    external
    componentSet(managersToHubs[msg.sender])
    componentNotSet(managersToRoutes[msg.sender].vault)
    amguPayable(false)
    payable`

This external function creates the Vault spoke of a water<b>melon</b> fund and adds the resulting contract address to the `managersToRoutes` mapping, mapped to the `msg.sender` (manager). The function implements the `componentNotSet()` modifier ensuring its one-time execution in the water<b>melon</b> fund setup process. The function is `payable` and also implements the `amguPayable` modifier, requiring amgu payment. Here, the `deductIncentive` parameter is set to `false`.
&nbsp;

`function completeSetup()
    external
    amguPayable(false)
    payable`

This external function completes the set up of a water<b>melon</b> fund. The function ensure the `childExists` mapping does not contain the `hub` address, then adds the `hub` address. The function then sets all of the `hub`'s spokes, routing, permissions and then adds the `hub` address to the `funds` state variable address array. The new water<b>melon</b> fund is then registered with the Version Registry. The function finally emits the `NewFund()` event, logging all parameter values as specified above. The function implements the `componentNotSet()` modifier ensuring its one-time execution in the water<b>melon</b> fund setup process. The function is `payable` and also implements the `amguPayable` modifier, requiring amgu payment. Here, the `deductIncentive` parameter is set to `false`.
&nbsp;

`function getFundById(uint withId) external view returns (address)`

This external view function returns the address of the water<b>melon</b> fund given the `withId` value provided, which is the index of of the `funds` address array state variable.
&nbsp;

`function getLastFundId() external view returns (uint)`

This external view function returns the index position of the most recently added water<b>melon</b> fund to the `funds` array state variable.
&nbsp;

`function engine() public view returns (address)`

This public view function returns the address of the water<b>melon</b> Engine contract.
&nbsp;

`function mlnToken() public view returns (address)`

This public view function returns the address of the MLN token contract.
&nbsp;

`function priceSource() public view returns (address)`

This public view function returns the address of the PriceSource contract.
&nbsp;

`function version() public view returns (address)`

This public view function returns the address of the Version contract.
&nbsp;

`function registry() public view returns (address)`

This public view function returns the address of the Registry contract.
&nbsp;

## FundRanking.sol

#### Description

The contract defines a function which returns specified information on each water<b>melon</b> fund created under a specific Version.
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

None.

&nbsp;

#### Events

None.

&nbsp;

#### Public State Variables

None.

&nbsp;

#### Public Functions

`function getFundDetails(
  address _factory)
external
view
returns(
  address[],
  uint[],
  uint[],
  string[],
  address[])`

This public view function gathers and returns the following water<b>melon</b> platform details for a specific Version, given the FundFactory address provided:

`address[]` - An exhaustive array of Hub (i.e. water<b>melon</b> fund) addresses.
`uint[]` - An exhaustive array of each water<b>melon</b> fund's current share price.
`uint[]` - An exhaustive array of each water<b>melon</b> fund's creation time.
`string[]` - An exhaustive array of each water<b>melon</b> funds' name.
`address[]` - An exhaustive array of each water<b>melon</b> fund's denomination asset token address.
&nbsp;
